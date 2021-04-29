
### 1. New deployment
Copy apis project to /apps/apis directory
```
$ scp -r ecollect_apis root@51.117.54.216://apps/apis/
```

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
$ pm2 restart ecosystem.config.js

#### Reload all
$ pm2 reload ecosystem.config.js

#### Delete all
$ pm2 delete ecosystem.config.js

## 2. Update
For updates\
copy the updated .js ans .json files into /app/apis/ecollect_apis/common/models
```
$ cp filename.js filename.json /app/apis/ecollect_apis/common/models
```
If its a new model, edit model.json filea and add the model details
```
$ vi /app/apis/ecollect_apis/server/model.json
"modelname": {
    "dataSource": "oracle",
    "public": true
  }
```
restart pm2
```
$ pm2 start ecosystem.config.js --only ecollect_apis
```
