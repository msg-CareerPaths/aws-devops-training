# (Optional) Serverless authentication

Goal: move the user authentication and management outside of the application.

## Required Reading
- [Cognito User Pools introduction](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html)
- [Managing Cognito users](https://docs.aws.amazon.com/cognito/latest/developerguide/managing-users.html)
- [Authentication with a user pool](https://docs.aws.amazon.com/cognito/latest/developerguide/authentication.html)
- [How to secure Spring boot REST API endpoints with Amazon Cognito](https://dev.to/aws-builders/how-to-secure-spring-boot-rest-api-endpoints-with-amazon-cognito-2fkl)

## Online Shop

![Application Diagram](https://raw.githubusercontent.com/msg-CareerPaths/aws-devops-training/master/chapters/diagrams/430.drawio.svg)

Create a Cognito User Pool via IaC
- Adjust the IaC stack to create a new Cognito User Pool.
- Also create a couple of test users. You can do it via IaC (terraform has built-in resources for this; for CDK you'd likely need to use a custom resource) or by hand.
- Create an app client that allows the `implicit` grant and whitelists both localhost and your CloudFront domain as the redirect URLs.

Adjust the frontend
- Add the amazon-cognito-identity-js library to the UI.
- Add [build-time environment variables](https://create-react-app.dev/docs/adding-custom-environment-variables/) to the UI, containing the Cognito User Pool ID, the Client ID and the AWS region.
- Adjust the UI logic such that, instead of sending the login credentials to the backend, it uses the CognitoUser#authenticateUser method to login against Cognito. 
- Adjust the UI logic to use the CognitoUserPool#getCurrentUser and CognitoUser#getSession methods to check if the user is already logged in (amazon-cognito-identity-js stores the tokens in the background in localStorage).
- Adjust all the other [UI-to-backend requests](https://github.com/msg-CareerPaths/aws-devops-demo-app/blob/27ed10b24b5ca3396dde92c808acce73d6585c2b/ui/src/store.ts#L171) to send the JWT token of the current user to the backend as a [bearer token](https://security.stackexchange.com/a/120244).
- Fill in the build-time environment variables in the UI's CI/CD.

Adjust the backend
- Add the spring-boot-starter-oauth2-resource-server library to the backend.
- Adjust the backend task definition to fill in the JWT issuer URI from environment variables (`SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUERURI`).
- Adjust the backend configuration to no longer use form-based login, but instead to use the `.oauth2ResourceServer().jwt()` authentication.

Testing
- Run the application locally. 
- Check that the UI logs in against Cognito and sends a JWT token to the backend via a header.
- Check that the backend accepts requests with this header from the UI.
- Alter the JWT from the UI code (e.g. add some random string to it), check that the backend rejects requests with this altered JWT.
- Deploy.
- Check that the login and backend communication still works on the cloud.

## Further Resources
 - [Simple and Secure User Authentication for a Web Application using AWS ALB Authentication, Cognito](https://medium.com/sysco-labs/simple-and-secure-user-authentication-for-a-web-application-using-aws-alb-authentication-cognito-939ee259e405)
 - [Amazon Cognito Identity SDK for JavaScript](https://github.com/aws-amplify/amplify-js/tree/main/packages/amazon-cognito-identity-js)
 - [How to Use Cognito for Web Applications](https://www.freecodecamp.org/news/how-to-use-cognito-for-web-applications/)