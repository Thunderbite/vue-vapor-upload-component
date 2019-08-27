# vue-vapor-upload-component

## Installation
npm install vue-vapor-upload-component

## Usage

Using this component can be done with this snippet:

```
<vue-vapor-upload-component
    accept=".csv"
    endpoint="/backstage/players"
    name="players-input"
    :reload-after-upload="true"
></vue-vapor-upload-component>
```

## Options

| Property | Description | Type | Default | Required |
| :--- | :--- | :--- | :--- | :--- |
| accept | List of accepted file types | string | empty | no |
| endpoint | URL where Vapor response will be posted to | string | empty | yes |
| name | Unique name for file input | string | empty | yes |
| reload-after-upload | Reload page after processing | string | empty | yes |
| upload-success-message | Message which will be sent with page reload | string | The upload was succcessful | no |


