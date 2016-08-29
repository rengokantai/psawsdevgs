#### psawsdevgs

######31 Load b
Add TCP 80 in sg for lb.  
2:38 Enable instance stickness  
A user should connect same instance in next request or they may lost session
######32
edit sg for vpc: inboud = lb sg
######36 S3 ov
Object Key: Folder Namw/Filename
######38 s3 command
```
aws s3 cp local s3://bucket/folder --recursive --exclude "patt"
```

######39 Connecting S3 nodejs
```
const AWS = require('aws-sdk');
const s3 =new AWS.S3();

module.exports.save = (name,data,cb)=>{
  let params = {
    Bucket:'',
    Key:`${name}.png`,
    Body:new Buffer(data,'base64'),
    ContentEncoding: 'base64',
    COntentType: 'image/png'
  }
  s3.putObject(params,(err,data)=>{
    cb(err,`//s3../bktname/${params.key}`);
  }
}
```
use
```
const s3Store = require('./imageS3');
function saveImage(name,base64String,cb){
  let id = base64String.split('data:image/png;base64,')[1];
  s3Store.save(name,id,cb);
}
module.exports.saveImage = saveImage;
```
######40 s3 cors
Add CORS Configuration->
```
<CORSConfiguration>
<CORSRule>
  <AllowedOrigin>*
  <AllowedMethod>GET
  <MaxAgeSeconds>3000
  <AllowedHeader>Authorization
</CORSRule>
</CORSConfiguration>
```


######41 Access S3 with EC2
EC2->launch config->create launch config->select customized ami->ser user data, (As text)
```
#! /bin/bash
npm start
```
Then IPaddress type ->(Assign a public ip to every instance)



######44 RDS overview
RDS read replica  
- non prod copy of db
- Eventual consistency with source
- useful for unning queries on data
- will not be used as failover! only for non-prod usage
######48
```
const pgClient = new sequelize(dbname,user,pass,{
  host:host,
  dialect:'postgres'
});
const tbname = pgClienct.define('tb',{
  id:{type:Sequelize.STRING,primaryKey:true},
  img;{type:Sequalize.STRING}
});
tbname.sync().then(()=>{})
```

######49 DynamoDB
provisioned throughput capacity  
read/wrote operations per second provisioned for dynamodb table
######50 RDS vs dynamo
d: storage flexibility  
r: query flexibility
######52 connect dynamodb (requires aws sdk)
```
const AWS = require('aws-sdk');
AWS.config.update({region:'us-west-2'});

const dynamodb=new AWS.DynamoDB();

function putItem (tb,item,cb){
  let params = {
    TableName:tb,
    Item:{}
  };
  
  for (let k of Object.keys(item)){
    let val;
    if(typeof item[k]==='string'){
      val = {S:item[k]}
    }else if(typeof item[k]==='number'){
      val = {N:''+item[k]};
    }else if(typeof item[k] instanceof Array){
      val = {SS:item[k]};
    }
    params.Item[k]=val
  }
  dynamodb.putItem(params,cb);
}

function getAllItems(tb,cb){
  let params ={TableName:tb}
  dynamodb.scan(params,cb);
}

function getItem(tb,idName,id,cb){
  let params ={
    TableName:tb,
    Key:{}
  };
  params.Key[idName]={S:id};
  dynamodb.getItem(params,cb);
}
module.exports.putItem = putItem;
module.exports.getAllItems = getAllItems;
module.exports.getItem = getItem;
```

impl:
```
function dynamoItemToToppings(items){
  return items.map((item)=>{
    return new Topping(item.id.S,item.name.S,+item.order.N);
  })
}
```
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
first, create sg, source = custom from Ec2 sg in vpc.  
Elasticcache->cache subnet group-> create cache subnet group:select vpc, add 2 subnets  
then cache clusters->..->select subnet group, select sg
