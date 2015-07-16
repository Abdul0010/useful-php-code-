1. Sending sms
While working on web or mobile applications, you often face a situation where you need to send an SMS
to your user either for login purposes or providing them with some information. The below PHP function
would help you with it.
For sending SMS using any language, you'd need an SMS gateway. Most of the SMS providers these
days provide with an API. For the below PHP snippet, I am using MSG91 as SMS gateway.
function send_sms($mobile,$msg)
{
 $authKey = "XXXXXXXXXXX";
date_default_timezone_set("Asia/Kolkata");
 $date = strftime("%Y-%m-%d %H:%M:%S");
//Multiple mobiles numbers separated by comma
 $mobileNumber = $mobile;
//Sender ID,While using route4 sender id should be 6 characters long.
 $senderId = "IKOONK";
//Your message to send, Add URL encoding here.
 $message = urlencode($msg);
//Define route
 $route = "template";
//Prepare you post parameters
 $postData = array(
 'authkey' => $authKey,
 'mobiles' => $mobileNumber,
 'message' => $message,
 'sender' => $senderId,
 1 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 'route' => $route
);
//API URL
 $url="https://control.msg91.com/sendhttp.php";
// init the resource
 $ch = curl_init();
 curl_setopt_array($ch, array(
 CURLOPT_URL => $url,
 CURLOPT_RETURNTRANSFER => true,
 CURLOPT_POST => true,
 CURLOPT_POSTFIELDS => $postData
 //,CURLOPT_FOLLOWLOCATION => true
));
//Ignore SSL certificate verification
 curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
 curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
//get response
 $output = curl_exec($ch);
 //Print error if any
 if(curl_errno($ch))
 {
 echo 'error:' . curl_error($ch);
 }
 curl_close($ch);
}
You'd see that I have highlighted two lines. On the first highlighted line you need to enter your passkey
and on the second you need to enter your senderID. While entering mobile number you need to specify
the country code (For example, USA's country code is 1. India's country code is 91).
Syntax:
<?php
$message = "Hello World";
$mobile = "918112998787";
 2 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
send_sms($mobile,$message);
?>
