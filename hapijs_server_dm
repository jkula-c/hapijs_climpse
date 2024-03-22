'use strict';

const Hapi = require('hapi'),
    hapiJwAuth = require('hapi-auth-jwt'),
    Boom = require('boom'),
    Crumb = require('crumb'),
    Inert = require('inert'),
    validateToken = require('./api/users/util/userFunctions').validate,
    Scooter = require('scooter'),
    Blankie = require('blankie'),
    glob = require('glob'),
    path = require('path'),
    secret = require('./config'),
    config = require('./config');


const server = new Hapi.Server();
/*
* 'Access-Control-Allow-Origin': 'http://localhost:63342'
* */

server.connection({
    port: process.env.PORT || 8080,
    routes: {
        cors:  {
            origin: ['*'],
            maxAge: 49002,
            headers: ['Accept', 'Authorization', 'Content-Type', 'If-None-Match', 'Accept-language']
        }
    }
});
// Registering hapi-jwt-auth
server.register([
    Scooter, {
          register: Blankie,
          options: {
              defaultSrc: 'none',
              scriptSrc: 'self',
              imgSrc: 'self',
              styleSrc:'self',
              childSrc: 'self',
              frameSrc: 'self'

          }

        },
        {
            register: hapiJwAuth
        }, {
            register: Inert

        }], (err) => {
            if (err){
                throw err;
            }
            // verifying the
            server.auth.strategy('jwt', 'jwt', 'required', {
                key: secret.secret,
                validateFunc: validateToken,
                verifyOptions: { algorithm: ['HS256'] }
            });
            //auth default
           // server.auth.default('jwt');
           // Looking through the routes in all subdirectories
            // and createa a new route for each file
            glob.sync('api/**/routes/*.js', {
                root: __dirname
            }).forEach(file => {
                const route = require(path.join(__dirname, file));
                server.route(route);
            });
        });

        server.start((err) => {
            if (err){
                throw err;
            }
            //Loging server info
            console.log(server.info.uri);
            const Udb = require('./db');
        });
