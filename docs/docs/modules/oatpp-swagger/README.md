---
title: oatpp-swagger (module)
description: Swagger UI. Auto-generate api-docs for oatpp Api Controllers.
sidebarDepth: 0
---

# oatpp-swagger <seo/>

[Github Repository](https://github.com/oatpp/oatpp-swagger)  
[Example Project](/examples/crud/)

**oatpp-swagger** - extension of [oatpp](/docs/modules/oatpp/) module.  
It provides [Swagger-UI](https://swagger.io/tools/swagger-ui/) for oatpp applications.  
Supports both "Simple" and "Async" oatpp APIs.

## Brief

- Use ```oatpp::swagger::Controller``` with ```oatpp::web::server::HttpConnectionHandler```
- Use ```oatpp::swagger::AsyncController``` with ```oatpp::web::server::AsyncHttpConnectionHandler```

- Swagger UI location - ```http://localhost:<PORT>/swagger/ui```
- OpenApi 3.0.0 specification location - ```http://localhost:<PORT>/api-docs/oas-3.0.0.json```

If you are using ```oatpp::web::server::api::ApiController``` most parts of your endpoints are documented automatically like:

- Endpoint name
- Parameters
- Request Body

You may add more information to your endpoint like follows:

```cpp
ENDPOINT_INFO(createUser) {
  info->summary = "Create new User";
  info->addConsumes<Object<UserDto>>("application/json");
  info->addResponse<Object<UserDto>>(Status::CODE_200, "application/json");
}
ENDPOINT("POST", "demo/api/users", createUser,
         BODY_DTO(Object<UserDto>, userDto)) {
  return createDtoResponse(Status::CODE_200, m_database->createUser(userDto));
}
```

More about endpoint annotation and API documentation [read here](/docs/components/api-controller/#endpoint-annotation-and-api-documentation).

### How to add Swagger UI to your project

1) Add ```oatpp::swagger::DocumentInfo``` and ```oatpp::swagger::Resources``` components to your AppComponents:

```cpp
/**
 *  General API docs info
 */
OATPP_CREATE_COMPONENT(std::shared_ptr<oatpp::swagger::DocumentInfo>, swaggerDocumentInfo)([] {

  oatpp::swagger::DocumentInfo::Builder builder;

  builder
  .setTitle("User entity service")
  .setDescription("CRUD API Example project with swagger docs")
  .setVersion("1.0")
  .setContactName("Ivan Ovsyanochka")
  .setContactUrl("https://oatpp.io/")

  .setLicenseName("Apache License, Version 2.0")
  .setLicenseUrl("http://www.apache.org/licenses/LICENSE-2.0")

  .addServer("http://localhost:8000", "server on localhost");

  return builder.build();

}());


/**
 *  Swagger-Ui Resources (<oatpp-examples>/lib/oatpp-swagger/res)
 */
OATPP_CREATE_COMPONENT(std::shared_ptr<oatpp::swagger::Resources>, swaggerResources)([] {
  // Make sure to specify correct full path to oatpp-swagger/res folder !!!
  return oatpp::swagger::Resources::loadResources("<YOUR-PATH-TO-REPO>/lib/oatpp-swagger/res");
}());

```

2) Create ```oatpp::swagger::Controller``` with list of endpoints you whant to document and add it to router:

```cpp
auto swaggerController = oatpp::swagger::Controller::createShared(<list-of-endpoints-to-document>);
router->addController(swaggerController);
```

**Done!**
