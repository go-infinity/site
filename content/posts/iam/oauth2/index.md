---
title: "IAM Concepts - OAuth2 and OIDC"
date: 2021-02-09T21:51:13+01:00
draft: false
hideLastModified: true
summaryImage: "iam.png" 
keepImageRatio: true
tags: ["iam", "auth2"]
summary: "Identity ad Access Management"
---

Most of the applications these days doesn't manage the authentication/authorization on its own and delegate this to some popular third party identity providers like `Google`, `Facebook`, `Github`. This is based on some popular authenitcation and authorization protocols such as `OAuth2`,`OIDC`, `SAML` etc. Advantages of this is many folds.
- Users don't need to manage multiple accounts for each application.
- Application doesn't need to rollout Identity and Access Management of its own which can be quite complex to manage.
- Easy onboarding of users.

Before going into details for each of these protocols, lets first set the definitions of some basic terms/actors in the overall process of Authentication and authorization.

___Authentication___: Authentication means verifying that someone is indeed who they claim to be.

___Authorization___: Authorization means deciding which resources a user can access, and what they should be allowed to do with those resources.

___Resource Owner___: The owner of the resource.

___Resource Server___: Resource Servers host resources of different users.

___Client___: It is the application accessing the resource server.

___Authorization Server___: It is the server issuing access tokens to the client after successful authorization.
