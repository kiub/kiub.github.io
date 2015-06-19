---
title: Slim PHP Framework
---

Two years back we had published an article called [create a RESTful Services using PHP](http://www.9lessons.info/2012/05/create-restful-services-api-in-php.html), in that most the PHP methods got depreciated. I have been looking for a simple RESTful api framework in PHP, I found few lightweight frameworks called [Slim](http://www.slimframework.com/) and [Epiphany](https://github.com/jmathai/epiphany). In this tutorial I had implement a sample user updates RESTful web services project using Slim framework in PHP, it is very simple to implement and only focused on RESTful.</div>

* [Download Script](http://demos.9lessons.info/url.php?url=https://app.box.com/s/19kwdm68xg2d2sltnbtg)     
* [Live Demo](http://demos.9lessons.info/project/index.php)

The tutorial contains three folders called _api_, _js_ and _css_ with PHP files.

```
**api**
 -- **Slim** <span style="color: orange;">// Slim Framework
 ---- _db.php_ 
---- _index.php_ <span style="color: orange;">// api index file
 ---- _.htaccess _<span style="color: orange;">// url redirection file. 
 **js**
 -- _jquery.min.js_
 -- _ajaxGetPost.js_
 **css**
 -- _style.css_
 index.html
```

**Database Design**
To build the friend updates system, you have to create three tables such as Users, Friends and Updates. You can check my previous tutorials for friend system.

## Users Table

User table contains all the users registration details.

```sql
CREATE TABLE `users` (
 `user_id` int(11) AUTO_INCREMENT,
 `username` varchar(50),
 `username` varchar(100),
 `name` varchar(100),
 `profile_pic` varchar(200),
 PRIMARY KEY (`user_id`)
 );
```

## Updates Table

User table contains user status update details.

```sql
CREATE TABLE `updates` (
 `update_id` int(11) AUTO_INCREMENT,
 `user_update` text,
 `user_id_fk` int(11), 
`created` int(11),
 `ip` varchar(50),
 PRIMARY KEY (`update_id`)
 );
```

[RESTful Web Services API using Java and MySQL](http://www.9lessons.info/2012/09/restful-web-services-api-using-java-and.html)

## Slim Framework

**api/index.php**
Slim Framework helps you to implement API system simple.

```php
<?php
require'db.php';
require 'Slim/Slim.php';
\Slim\Slim::registerAutoloader();

$app = new \Slim\Slim();

$app->get('/users','getUsers');
$app->get('/updates','getUserUpdates');
$app->post('/updates', 'insertUpdate');
$app->delete('/updates/delete/:update_id','deleteUpdate');
$app->get('/users/search/:query','getUserSearch');

$app->run();


function getUsers() {
    $sql = "SELECT user_id,username,name,profile_pic FROM users ORDER BY user_id DESC";
    try {
        $db = getDB();
        $stmt = $db->query($sql); 
        $users = $stmt->fetchAll(PDO::FETCH_OBJ);
        $db = null;
        echo '{"users": ' . json_encode($users) . '}';
    } catch(PDOException $e) {
        //error_log($e->getMessage(), 3, '/var/tmp/phperror.log'); //Write error log
        echo '{"error":{"text":'. $e->getMessage() .'}}';
    }
}

function getUserUpdates() {
    $sql = "SELECT A.user_id, A.username, A.name, A.profile_pic, B.update_id, B.user_update, B.created FROM users A, updates B WHERE A.user_id=B.user_id_fk  ORDER BY B.update_id DESC";
    try {
        $db = getDB();
        $stmt = $db->prepare($sql);
        $stmt->execute(); <span class="Apple-tab-span" style="white-space: pre;"> 
        $updates = $stmt->fetchAll(PDO::FETCH_OBJ);
        $db = null;
        echo '{"updates": ' . json_encode($updates) . '}';
    } catch(PDOException $e) {
        echo '{"error":{"text":'. $e->getMessage() .'}}';
    }
}

**<span style="color: #674ea7;">// DELETE http://www.yourwebsite.com/api/updates/delete/10**
 function deleteUpdate($update_id)
 {
 $sql = "DELETE FROM updates WHERE update_id=<span style="color: #444444;">:update_id";
 try {
 $db = getDB();
 $stmt = $db->prepare($sql); 
$stmt->bindParam("update_id", $update_id);
 $stmt->execute();
 $db = null;
 echo true;
 } catch(PDOException $e) {
 echo '{"error":{"text":'. $e->getMessage() .'}}';
 }
 }

**<span style="color: #674ea7;">// POST http://www.yourwebsite.com/api/updates**
 function insertUpdate() {
 <span style="color: orange;">//....
 <span style="color: orange;">//....
 }

function getUserUpdate($update_id) {
 <span style="color: orange;">//.....
 <span style="color: orange;">//.....
 }

**<span style="color: #674ea7;">// GET http://www.yourwebsite.com/api/users/search/sri**
 function getUserSearch($query) {
 <span style="color: orange;">//.....
 <span style="color: orange;">//....
 }
 **?>**</div>

**db.php**
 You have to modify database configuration details, before trying this enable _php_pdo_ extension in _php.ini_ file.

```php
<?php**
 function getDB() {
 $dbhost="localhost";
 $dbuser="username";
 $dbpass="username";
 $dbname="database";
 $dbConnection = new PDO("mysql:host=$dbhost;dbname=$dbname", $dbuser, $dbpass); 
$dbConnection->setAttribute(PDO::<span style="color: #3d85c6;">ATTR_ERRMODE, PDO::<span style="color: #3d85c6;">ERRMODE_EXCEPTION);
 return $dbConnection;
 }
 **?>**</div>
```

**Chrome Extention**
 A Extention for testing PHP restful API response download here [Advanced REST client Application](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo)

## Cross Domain Access

A cross-domain system is transfer information/data between two or more differing domains. Eg. _abc.com_ to _xyz.com_

**.htaccess** 
I did modified the Slim Framework default .htaccess code for cross domain support.

RewriteEngine On

<span style="color: orange;"># Some hosts may require you to use the `RewriteBase` directive.
 <span style="color: orange;"># If you need to use the `RewriteBase` directive, it should be the
 <span style="color: orange;"># absolute physical path to the directory that contains this htaccess file.
 <span style="color: orange;">#
 <span style="color: orange;"># RewriteBase /

<span style="color: orange;"># Cross domain access
 Header <span style="color: #38761d;">add Access-Control-Allow-Origin "*"
 Header <span style="color: #38761d;">add Access-Control-Allow-Headers "origin, x-requested-with, content-type"
 Header <span style="color: #38761d;">add Access-Control-Allow-Methods "PUT, <span style="color: #3d85c6;">GET, POST, <span style="color: #38761d;">DELETE, <span style="color: #351c75;">OPTIONS"

RewriteCond %{<span style="color: #38761d;">REQUEST_FILEname} !-f
 RewriteRule ^(.*)$ index.php [QSA,L]</div>

Learn more about [Status Message Design with CSS](http://www.9lessons.info/2010/12/status-message-design-with-css.html)

**Jquery**
 Contains JavaScript and HTML code, using Jquery ajax parsing API data into HTML format.

<script src="<span style="color: #3d85c6;">http://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
 <script src="<span style="color: #3d85c6;">js/ajaxGetPost.js"></script>
 <script>
 $(document).ready(function()
 {

<span style="color: #0b5394;">**var** base_url="<span style="color: #3d85c6;">http://www.yourwebsite.com/project_name/";
 <span style="color: #0b5394;">**var** url,encodedata;
 $("#update").focus();

<span style="color: orange;">/* Load Updates */
 url=base_url+'api/updates';
 ajax_data('GET',url, function(data)
 {
 $.each(data.updates, function(i,data)
 {
 <span style="color: #0b5394;">**var** html="<div class='stbody' id='stbody"+data.update_id+"'><div class='stimg'><img src='"+data.profile_pic+"' class='stprofileimg'/></div><div class='sttext'><strong>"+data.name+"</strong>"+data.user_update+"<span id='"+data.update_id+"' class='stdelete'><span style="color: #444444;">Delete</div></div>";
 $(html).appendTo("#mainContent");
 });
 });

<span style="color: orange;">/* Insert Update */
 $('body').on("click",'.stpostbutton',function()
 {
 **var** update=$('#update').val();
 encode=JSON.stringify({
         "<span style="color: #3d85c6;">user_update": update,
         "<span style="color: #3d85c6;">user_id": $('#user_id').val()
         });
 url=base_url+'api/updates';
 if(update.length>0)
 {
 post_ajax_data(url,encode, function(data)
 {
 $.each(data.updates, function(i,data)
 {
 <span style="color: #0b5394;">**var** html="<div class='stbody' id='stbody"+data.update_id+"'><div class='stimg'><img src='"+data.profile_pic+"' class='stprofileimg'/></div><div class='sttext'><strong>"+data.name+"</strong>"+data.user_update+"<span id='"+data.update_id+"' class='stdelete'><span style="color: #444444;">Delete</div></div>";
 $("#mainContent").prepend(html);
 $('#update').val('').focus();
 });
 });
 }

});

<span style="color: orange;">/* Delete Updates */
 $('body').on("click",'.stdelete',function()
 {
 var ID=$(this).attr("id");
 url=base_url+'api/updates/delete/'+ID;
 ajax_data('DELETE',url, function(data)
 {
 $("#stbody"+ID).fadeOut(<span style="color: #3d85c6;">"slow");
 });
 });

});
 </script></div>

<div>![Create a RESTful services using Slim PHP Framework](https://lh6.googleusercontent.com/-hZlpWcC8xsc/VIYUv6vELMI/AAAAAAAAMMQ/1D8CLZ5qTlY/s534/one.PNG)</div>

**HTML Code**
 Contains HTML code. _$("body").on('click','.stpostbutton', function(){}_- _stpostbutton_ is the ID name of the POST button. Using jquery stringify converting input data into JSON format.

<span style="color: #3d85c6;"><div>
 <textarea id="update" class="stupdatebox"></textarea>
 <span style="color: orange;"><input type="hidden<span style="color: orange;">" id="user_id<span style="color: orange;">" value="User Session Value<span style="color: orange;">">
 <span style="color: orange;"><input type="submit<span style="color: orange;">" value="POST<span style="color: orange;">" class="stpostbutton<span style="color: orange;">">
 <span style="color: #3d85c6;"></div>
 <span style="color: #3d85c6;">
<span style="color: #3d85c6;"><div id="mainContent<span style="color: #3d85c6;">"></div></div>

**ajaxGetPost.js**
 Jquery Post, Get and Delete Ajax functions.

<span style="color: orange;">//POST Ajax
 function post_ajax_data(url, encodedata, success)
 {
 $.ajax({
 type:"POST",
 url:url,
 data :encodedata,
 dataType:"json",
 restful:true,
 contentType: 'application/json',
 cache:false,
 timeout:20000,
 async:true,
 beforeSend :function(data) { },
 success:function(data){
 success.call(this, data);
 },
 error:function(data){
 alert("Error In Connecting");
 }
 });
 }

<span style="color: orange;">//GET and DELETE Ajax
 function ajax_data(type, url, success)
 {
 $.ajax({
 type:type,
 url:url,
 dataType:"json",
 restful:true,
 cache:false,
 timeout:20000,
 async:true,
 beforeSend :function(data) { },
 success:function(data){
 success.call(this, data);
 },
 error:function(data){
 alert("Error In Connecting");
 }
 });
 }

### Related Posts

*   [JSON Input String using JavaScript.](http://www.9lessons.info/2013/02/json-input-string-using-javascript.html)
*   [RESTful Web Services API using Java and MySQL](http://www.9lessons.info/2012/09/restful-web-services-api-using-java-and.html)
*   [Create a RESTful Services API in PHP.](http://www.9lessons.info/2012/05/create-restful-services-api-in-php.html)
*   [Login with Google Plus OAuth.](http://www.9lessons.info/2011/10/login-with-google-plus-oauth.html)
*   [Google Materialize a Responsive HTML5 Framework](http://www.9lessons.info/2015/01/google-materialize-responsive-html5.html)
*   [jQuery Mobile Framework Tutorial.](http://www.9lessons.info/2011/12/jquery-mobile-framework-tutorial.html)
*   [Getting Started with Ruby on Rails](http://www.9lessons.info/2011/02/getting-start-with-ruby-on-rails.html)
*   [Custom Audio Player with Jquery Audio Controls Plugin](http://www.9lessons.info/2015/05/custom-audio-player-with-jquery-audio.html)
*   [Google Charts with Jquery Ajax](http://www.9lessons.info/2015/04/google-charts-with-jquery-ajax.html)
*   [Audio Recording with Custom Audio Player using Jquery and HTML5](http://www.9lessons.info/2015/03/audio-recording-with-custom-audio.html)
