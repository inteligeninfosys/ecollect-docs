
Generate pm2 process file
```
$ pm2 ecosystem
File /root/ecosystem.config.js generated
```
Edit ecosystem.config.js and add within the apps[] block ecollect_apis as below
```
$ vi ecosystem.config.js

module.exports = {
  apps : [
    {
    name       : "ecollect_apis",
    script     : "/app/apis/ecollect_apis",
    watch       : true
    }
  ],

  deploy : {
    production : {
      user : 'SSH_USERNAME',
      host : 'SSH_HOSTMACHINE',
      ref  : 'origin/master',
      repo : 'GIT_REPOSITORY',
      path : 'DESTINATION_PATH',
      'pre-deploy-local': '',
      'post-deploy' : 'npm install && pm2 reload ecosystem.config.js --env production',
      'pre-setup': ''
    }
  }
};
```
#### Start all applications
$ pm2 start ecosystem.config.js

#### Start only the app named ecollect_apis
$ pm2 start ecosystem.config.js --only ecollect_apis

#### Stop all
$ pm2 stop ecosystem.config.js

#### Restart all
$ pm2 start   ecosystem.config.js
#### Or
$ pm2 restart ecosystem.config.js

#### Reload all
$ pm2 reload ecosystem.config.js

#### Delete all
$ pm2 delete ecosystem.config.js
