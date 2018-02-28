# passport-oauth2-cognito
[Passport](https://github.com/jaredhanson/passport) [strategy](https://github.com/jaredhanson/passport-strategy) for authenticating and fetching profile data from [AWS Cognito](https://aws.amazon.com/cognito/) [User pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html) using [OAuth2](https://oauth.net/2/) and the [Amazon SDK](https://aws.amazon.com/sdk-for-node-js/)

## Install

    $ npm install passport-oauth2-cognito
    
## Usage

#### Configure Strategy

The Cognito OAuth 2.0 authentication strategy authenticates requests using the 
OAuth 2.0 framework and retrieves user data from AWS Cognito User Pools.
The strategy requires a `verify` callback, which accepts these
credentials and calls `done` providing a user, as well as `options` specifying a
consumer key, consumer secret, and callback URL.
    
    const passport = require('passport')
    const OAuth2CognitoStrategy = require('passport-oauth2-cognito');
    
    const options = {
      callbackURL: 'https://myapp.com/auth/cognito/callback',
      clientDomain: 'https://myapp.auth.us-west-2.amazoncognito.com',
      clientID: '123-456-789',
      clientSecret: 'shhh-its-a-secret',
      region: 'us-west-2'
    };
   
    function verify(accessToken, refreshToken, profile, done) {
      User.findOrCreate(profile, (err, user) => {
        done(err, user);
      });
    }
    
    passport.use(new OAuth2CognitoStrategy(options, verify));  
    passport.serializeUser((user, done) => done(null, user));
    passport.deserializeUser((obj, done) => done(null, obj));
    
#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'oauth2-cognito'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/auth/cognito',
      passport.authenticate('oauth2-cognito')
    );
    app.get('/auth/cognito/callback',
      passport.authenticate('oauth2-cognito'),
      (req,res) => res.send(req.user)  
    );

## Cognito configuration

When you create your App Client, you will need to generate an App Client Secret

Your App client settings will need:

Enabled Identity Providers: Cognito User Pool

Callback URL(s): `options.callbackURL`

Allowed OAuth Flows: Authorization code grant

Allowed OAuth Scopes: [openid, aws.cognito.signin.user.admin, profile]

You must also configure a Domain name for use as `options.clientDomain`