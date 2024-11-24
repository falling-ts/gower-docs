---
title: Preface
url: "/en/single/preface"
---

> Note: For convenience in writing code, punctuation marks in the documentation default to English punctuation.

The focus is on quick startup, integrating commonly used tools from current open-source projects to enable rapid business development. The default project structure uses a Go template, which can be used to develop APIs as well as integrate front-end and back-end together.

## Project Background

This project is primarily for the author to learn Go language development, combining several years of Laravel development experience to achieve the ability to quickly produce deployable projects using Go.

## Brief Introduction

Gower is a rapid development framework based on Go/Gin, designed to minimize package dependencies internally to avoid circular reference issues.

The overall design focuses on business development, with the `app` package providing all structural instance services. The `app` package acquires various bound services through service providers. Service providers import service instances and service carriers, and then the `app` imports the service providers to bind the services.

Binding is primarily done using a `map[string]func (...services.Service) services.Service` mapping. When a service is needed, the function is executed, and the service is cached to enable dynamic service retrieval.

To illustrate this design with an analogy to a registration tree pattern: imagine the trunk of the tree is the `app` package, where we perform business development at the center of the trunk. The `app` package cannot reference our business code in reverse, but the business code can reference external service packages. The trunk obtains a service through branch nodes, and the branches that provide the service are service providers. The branches assemble actual services and their carriers through sub-branches to provide internal service functionality. Overall, it allows internal references to the external, but not vice versa.

## Current Service Function List

```yaml
auth: Stateless user authentication service using JWT
cache: Cache service
config: Configuration service
cookie: Built-in encrypted cookie service
db: GORM DB service
exception: Exception service
logger: Logging service
passwd: Password hashing service
response: Response body service
route: Gin routing service
symcrypt: Symmetric encryption service
translate: Translation service
upload: Upload service
util: Various utility method services
validator: Request parameter validation service
```


Most of these features have basic implementations, and more complex functionalities mostly inherit from third-party open-source features.

## About the Frontend

The frontend code differs significantly from traditional frontend practices, mainly because HTML files are no longer managed by the frontend. The frontend framework primarily handles library file packaging, meaning it only bundles JS and CSS files. HTML is provided through Go templates.

Frontend technology stack list:

```yaml
vite
typescript
vue
tailwindcss
stylus
daisyui
animate.css
autoprefixer
postcss
localforage
js-cookie
jquery
simplebar
```


For more detailed information, please refer to the `package.json` file in the project root directory.