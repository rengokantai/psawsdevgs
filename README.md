#### psawsdevgs
######57 Elastic Beanstalk
Diff between cloudformation and elastic beanstalk  
CF: only provisions resources  
EB: provisions resources and runs your app

######58 Deploying app Baenstalk
worker env: simple queue service  
web server env: web app  
zip all files with npm dependency downloaded
```
zip -r app.zip .
```
Additional resources -> create this environment inside a vpc  
Configuration Details -> Application health check URL: /  
VPC configuration: select VPS, associate public IP address, select all (ELB,EC2), select security group

######59 Configuring an Elastic Beanstalk Env
Troubleshooting.  
select app->left sidebar(logs)->Request logs->last 100 lines  

Change node.js version for AMI (from v4->v6)
left sidebar(configuration)->software configuration->change node version->apply
######63 Edging your app
Step2 Create distributions  
HTTP Port 80  
HTTPS Port 443  
Viewer protocol policy -> Redirect HTTP to HTTPS  
Allowed HTTP Methods->GET...DELETE  
Object caching(customize): MinimumTTL: 10  
(How long cloudfront will respond to request with its cached content before checking with the origin again)  
We need forward cookies because our node app is using cookie to log in, and elastic beanstalk lb is using cookie for server stickness(whitelist)
```
AWSELB
appname
```
Forward query strings -> Yes
######64 Configuring a cloudfront dist
Behavior->create behavior  
path pattern:
```
ke/*
```
redirect http to https, Only GET and POST

######66 Configuring a redis cluster
Elasticcache->cache subnet group->
