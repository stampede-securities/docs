# Application Development Pipeline

The stampede application pipeline currently has two stages: staging and production. In the future this will be expanded to three:
development, staging, and production. Until this point the purpose of each application environment has not been clearly defined,
and as such these environments have not been used for their intended functions. The purpose of this document is to clearly define
when to deploy to each environment and why.

## Production

The production environment is what will be shown to the world outside of Stampede, whether this is the audience of a publicly realeased app or the customer of an important demo. All code that enters this environment should be carefully reviewed in order to ensure that there are no errors in this branch. Code should be heavily tested before it goes to production so that public customers and users do not see development issues. As a result, deploys to production should happen the least frequently of all the environments. Deployments to applications should occur on a regular release schedule for new features, so as not to interrupt application service to users.

## Staging

Staging is currently used to test app features--for example mailing or push notifications--in a production-like environment. Faulty code in this branch is currently not an issue because only engineers at stampede are observing this environment. However, staging environments will be used for QA testing with a restricted public in the future. Staging should be free of any known bugs or application-breaking errors. The application should be theoretically ready for production, although QA testing will probably reveal minor bugs that should be fixed before release.
## Development

The development environment will serve the purpose of our current staging environment. This is the production-like playground for engineers to test anything. Only our internal engineers will see this version of the application so breaking it should not be an issue as long as a semi-stable staging version of the application can be released in time for scheduled QA testing.
