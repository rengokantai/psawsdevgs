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
