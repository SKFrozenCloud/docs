Amplify gives you the ability to limit which individuals or groups should have access to create, read, update, or delete data on your various schema types by specifying an `@auth` directive.

The following is a high level overview of the various authorization scenarios we support in the Amplify libraries.  Each scenario has a variety of options you can tune to fit the needs of your application.

* [**Owner Based Authorization**](#owner-based-authorization): Uses Cognito User Pools as an auth provider to add ownership and access rules to instances of your model
* [**Static Group Authorization**](#static-group-authorization): Uses Cognito User Pools as an auth provider with groups to apply access rules to instances of your model
* [**Owner and Static Group Combined**](#owner-and-static-group-combined): Uses a combination of both *Owner Based Authorization* and *Static Group Authorization* to control ownership and access.
* [**Public Authorization**](#public-authorization): Use API Key or IAM to allow public access to your model instances
* [**Private Authorization**](#private-authorization): Use IAM or Cognito User Pools to allow private access to your model instances
* [**Owner based Authorization with OIDC provider**](#owner-based-authorization-with-oidc-provider): Use a 3rd party OIDC Provider to achieve *Owner based authorization*
* [**Static Group Authorization with OIDC provider**](#static-group-authorization-with-oidc-provider): Use a 3rd party OIDC Provider to achieve *Static group authorization* using a custom `groupClaim`.

## Commonly used `@auth` rule patterns

### Owner Based Authorization

The following are commonly used patterns for owner based authorization.  For more information on how to tune these examples, please see the [CLI documentation on owner based authorization](~/cli/graphql-transformer/auth.md#owner-authorization).

* Create/update/delete mutations are private to the owner.
```graphql
type YourModel @model @auth(rules: [{ allow: owner }]) {
  ...
}
```

* Allow others to update and read, but not delete.
```graphql
type YourModel @model @auth(rules: [{ allow: owner,
                                   operations: [create, delete]}]) {
  ...
}
```

### Static Group Authorization
The following are commonly used patterns for static group authorization.  For more information on how to tune these examples, please see the [CLI documentation on static group authorization](~/cli/graphql-transformer/auth.md#static-group-authorization).

* Users belonging to the "Admin" group can CRUD (create, read, update, and delete), others can not access anything.
```graphql
type YourModel @model @auth(rules: [{ allow: groups,
                                      groups: ["Admin"] }]) {
  ...
}
```

* Users belonging to the "Admin" group can CRUD, others query and update.
```graphql
type YourModel @model @auth(rules: [{ allow: groups,
                                       groups: ["Admin"],
                                   operations: [create, delete] }]) {
  ...
}
```

### Owner and Static Group Combined
The following are commonly used patterns for combining owner and static group authorization.  For more information on how to tune these examples, please see the [CLI documentation on static group authorization](~/cli/graphql-transformer/auth.md#static-group-authorization).

* Users have their own data, but users who belong to the `Admin` group have access to their data and anyone else in that group.  Users in the `Admin` group have the ability to make mutation on behalf of users not in the `Admin` group
```graphql
type YourModel @model @auth(rules: [{ allow: owner },
                                      { allow: groups, groups: ["Admin"]}]) {
  ...
}
```

### Public Authorization
The following are commonly used patterns for public authorization.  For more information on how to tune these examples, please see the [CLI documentation on static group authorization](~/cli/graphql-transformer/auth.md#static-group-authorization#public-authorization).

* Auth provider is API Key, and all data is public
```graphql
type YourModel @model @auth(rules: [{ allow: public }]) {
  ...
}
```

* Auth provider is IAM, and all data is public
```graphql
type YourModel @model @auth(rules: [{ allow: public, provider: iam }]) {
  ...
}
```

### Private Authorization
The following are commonly used patterns for private authorization.  For more information on how to tune these examples, please see the [CLI documentation on static group authorization](~/cli/graphql-transformer/auth.md#static-group-authorization#private-authorization).

* Cognito user pool authenticated users can CRUD all posts, regardless of who created it.  Guest users do not have access.
```graphql
type YourModel @model @auth(rules: [{ allow: private }]) {
  ...
}
```
* IAM authenticated users can CRUD all posts, regardless of who created it.  Guest users do not have access:
```graphql
type YourModel @model @auth(rules: [{ allow: private, provider: iam }]) {
  ...
}
```

### Owner based Authorization with OIDC provider
The following are commonly used patterns for owner based authorization using a 3rd party OIDC provider.  For more information on how to tune these examples, please see the [CLI documentation on static group authorization](~/cli/graphql-transformer/auth.md#authorization-using-an-oidc-provider).

* Using a 3rd party OIDC provider to achieve owner based authorization.
```graphql
type YourModel @model @auth(rules: [{ allow: owner,
                                     provider: oidc,
                                identityClaim: "sub" }]) {
  ...
}
```

### Static Group Authorization with OIDC provider
The following are commonly used patterns for using `groupClaims` to achieve group based authorization using a 3rd party OIDC provider.  For more information on how to tune these examples, please see the [CLI documentation on static group authorization](~/cli/graphql-transformer/auth.md#custom-claims).

* Using a custom value for `groupClaim` to achieve static group authorization with a 3rd party OIDC provider.
```graphql
type YourModel @model @auth(rules: [{ allow: groups
                                     provider: oidc
                                       groups: ["Admin"]
                                   groupClaim: "https://myapp.com/claims/groups"
                                      }]) {
  ...
}
```