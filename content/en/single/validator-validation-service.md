---
title: validator-Validation Service
url: "/en/single/validator-validation-service"
---

## Used Open Source Libraries

```yaml
"github.com/go-playground/validator/v10"
```


## Service Provider

### Service Identifier

```yaml
"validator"
```


### Register Service

```go
P.Register("validator", func(...services.Service) services.Service {
    return validator.New().Init()
})
```


### Registration Format Refer to auth Service

## Dependent Services

No dependencies

## ENV Configuration

No related environment configuration

## Service Interface Methods

```go
type ValidatorService interface {
	Service // Common service interface
	
	Translate(errs validator.ValidationErrors) validator.ValidationErrorsTranslations // Customize the return of multiple validation errors
	GetTrans() ut.Translator // Get the validator
}
```


## Usage Example

The validation methods have already been configured in `app/request.go`, generally no modification is needed. You can use the validation directly according to the tutorial in the "Basic Content - Request Validator".