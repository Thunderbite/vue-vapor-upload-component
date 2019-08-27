<template>
    <div>
        <div :class="'alert alert-' + alert.type" role="alert" v-if="alert.message" v-html="alert.message"></div>

        <div v-if="upload.active">
            <div class="progress">
                <div class="progress-bar" role="progressbar" :aria-valuenow="upload.progress" aria-valuemin="0" aria-valuemax="100" :style="'width: ' + upload.progress + '%;'">
                    <span class="sr-only">{{ upload.progress }}% complete</span>
                </div>
            </div>
        </div>

        <form class="form-horizontal" v-on:submit.prevent="uploadFile" v-show="uploadComponentVisible">

            <label :for="name" class="control-label">
                <span class="btn btn-primary">
                    <span v-if="file === null">
                        <i class="fa fa-plus"></i> Select file
                    </span>
                    <span v-else>
                        {{ file }}
                    </span>
                    <input type="file" :id="name" ref="file" :accept="accept" required @change="onFileChange">
                </span>
            </label>
            
            <button type="submit" class="btn btn-success" v-if="file !== null">
                Upload
            </button>
            
        </form>

    </div>
</template>

<script>
export default {
    
    // Props.
    props: {
        accept: {
            type: String,
            required: false,
            default: '',
        },
        endpoint: {
            type: String,
            required: true,
            default: '',
        },
        name: {
            type: String,
            required: true,
            default: '',
        },
        reloadAfterUpload: {
            type: Boolean,
            default: false,
        },
        uploadSuccesMessage: {
            type: String,
            required: false,
            default: 'The upload was succcessful',
        }, 
    },

    computed: {
        uploadComponentVisible() {
            return !this.upload.active && !this.upload.processing;
        }
    },

    data() {
        return {
            upload: {
                active: false,
                processing: false,
                progress: 0,
                success: null,
            },
            alert: {},
            file: null,
        }
    },

    mounted() {
        // Show alert when no endpoint is set.
        if(this.endpoint == "") {
            alert('Warning: no endpoint set in component props.');
        }

        // Show alert when no input ID is set.
        if(this.name == "" ) {
            alert('Warning: no name set in component props.');
        }
    },

    methods: {
        // Trigger file upload.
        uploadFile() {

            // Reset alert.
            this.alert = {}

            // Init upload.
            Vapor.store(this.$refs.file.files[0], {
                progress: progress => {
                    this.uploadStarted(progress);
                }
            }).then(response => {
                this.processingStarted();

                // Store response data in backend.
                axios.post(this.endpoint, {
                    uuid: response.uuid,
                    key: response.key,
                    bucket: response.bucket,
                    name: this.$refs.file.files[0].name,
                    content_type: this.$refs.file.files[0].type,
                }).then(response => {
                    this.processingCompleted();
                }).catch(error => {
                    this.processingFailed(error);
                });
            });
        },

        // Event when the processing has started.
        processingStarted() {
            // Set processing status.
            this.upload.active = false;
            this.upload.processing = true;
            
            this.showAlert('<i class="fa fa-spinner fa-spin"></i> Processing...', 'info');
        },

        // When the upload has started.
        uploadStarted(progress) {
            // Set upload status.
            this.upload.active = true;
            this.upload.progress = Math.round(progress * 100);
            
            this.showAlert('<i class="fa fa-spinner fa-spin"></i> Uploading...', 'info');
        },

        // Event after the processing has completed.
        processingCompleted() {
            // Mark upload as successful.
            this.upload.processing = false;
            this.file = null;
            
            // When the window should be reloaded.
            if(this.reloadAfterUpload) { 
                window.location.href = "https://" + window.location.host + window.location.pathname + '?success-message=' + this.uploadSuccesMessage;
            }
            
            // Set alert data.
            this.showAlert(this.uploadSuccesMessage, 'success');
        },

        // Event after processing has failed.
        processingFailed(error) {
            // When something went wrong.
            this.upload.processing = false;
            this.file = null;

            // Set alert data.
            this.showAlert(error.response.data, 'danger');
        },

        // Show alert.
        showAlert(message, type) {
            this.alert = {
                message: message,
                type: type,
            }
        },

        // When file input has changed.
        onFileChange(event) {
            // Get file data.
            var data = event.target.files[0];

            // Set file name.
            this.file = data.name;
        }
    },
}
</script>

<style scoped>
input[type="file"] {
    background: rgba(255, 255, 255, 0);
    overflow: hidden;
    position: fixed;
    width: 1px;
    height: 1px;
    z-index: -1;
    opacity: 0;
}
</style>