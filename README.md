# useful-php-code-
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
2. SENDIng email WITH mandrill
Mandrill is a powerful SMTP provider. Developers tend to use a third party SMTP provider for better
inbox deliveries.
For the below function you would have to put a file "Mandrill.php" in the same folder as the PHP file you
would be using to send emails.
function send_email($to_email,$subject,$message1)
{
 require_once 'Mandrill.php';
$apikey = 'XXXXXXXXXX'; //specify your api key here
$mandrill = new Mandrill($apikey);
$message = new stdClass();
$message->html = $message1;
$message->text = $message1;
$message->subject = $subject;
$message->from_email = "blog@koonk.com";//Sender Email
$message->from_name = "KOONK";//Sender Name
$message->to = array(array("email" => $to_email));
$message->track_opens = true;
$response = $mandrill->messages->send($message);
}
In the above code you would have to specify your own api key that you get from your Mandrill account.
Syntax
<?php
$to = "abc@example.com";
$subject = "This is a test email";
$message = "Hello World!";
send_email($to,$subject,$message);
?>
 3 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
For best deliveries, I would recommend you to configure DNS settings as guided by Mandrill.
3. PHP function to help prevent sql injection
SQL injection or SQLi is a common technique used to hack into a website. Using the below code can help
you prevent it.
function clean($input)
{
 if (is_array($input))
 {
 foreach ($input as $key => $val)
 {
 $output[$key] = clean($val);
 // $output[$key] = $this->clean($val);
 }
 }
 else
 {
 $output = (string) $input;
 // if magic quotes is on then use strip slashes
 if (get_magic_quotes_gpc())
 {
 $output = stripslashes($output);
 }
 // $output = strip_tags($output);
 4 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 $output = htmlentities($output, ENT_QUOTES, 'UTF-8');
 }
// return the clean text
 return $output;
}
Syntax
<?php
$text = "<script>alert(1)</script>";
$text = clean($text);
echo $text;
?>
Had we not used the clean function above, the page would have popped up an alert box.
4. detect location of the user
Using the below function you can check the city from which the user is visiting your website.
function detect_city($ip) {

 $default = 'UNKNOWN';
 $curlopt_useragent = 'Mozilla/5.0 (Windows; U; Windows NT 5.1;
 en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6 (.NET CLR 3.5.30729)';

 $url = 'http://ipinfodb.com/ip_locator.php?ip=' . urlencode($i
p);
 $ch = curl_init();

 $curl_opt = array(
 CURLOPT_FOLLOWLOCATION => 1,
 CURLOPT_HEADER => 0,
 CURLOPT_RETURNTRANSFER => 1,
 CURLOPT_USERAGENT => $curlopt_useragent,
 5 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 CURLOPT_URL => $url,
 CURLOPT_TIMEOUT => 1,
 CURLOPT_REFERER => 'http://' . $_SERVER['HTTP_HOST
'],
 );

 curl_setopt_array($ch, $curl_opt);

 $content = curl_exec($ch);

 if (!is_null($curl_info)) {
 $curl_info = curl_getinfo($ch);
 }

 curl_close($ch);

 if ( preg_match('{<li>City : ([^<]*)</li>}i', $content, $regs)
 ) {
 $city = $regs[1];
 }
 if ( preg_match('{<li>State/Province : ([^<]*)</li>}i', $conte
nt, $regs) ) {
 $state = $regs[1];
 }
 if( $city!='' && $state!='' ){
 $location = $city . ', ' . $state;
 return $location;
 }else{
 return $default;
 }

 }
Syntax
<?php
$ip = $_SERVER['REMOTE_ADDR'];
$city = detect_city($ip);
echo $city;
?>
 6 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
5. get the source code of a webpage
Using the function below you would be able to get the HTML code of any webpage.
function display_sourcecode($url)
{
$lines = file($url);
$output = "";
foreach ($lines as $line_num => $line) {
 // loop thru each line and prepend line numbers
 $output.= "Line #<b>{$line_num}</b> : " . htmlspecialchars($line) . "
<br>\n";
}
}
Syntax
<?php
$url = "http://blog.koonk.com";
$source = display_sourcecode($url);
echo $source;
?>
6. show number of people who have liked YOUR facebook page
function fb_fan_count($facebook_name)
{
 $data = json_decode(file_get_contents("https://graph.facebook.com/
".$facebook_name));
 $likes = $data->likes;
 return $likes;
}
Syntax
<?php
$page = "koonktechnologies";
$count = fb_fan_count($page);
echo $count;
 7 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
?>
7. determine dominant color of any image
function dominant_color($image)
{
$i = imagecreatefromjpeg($image);
for ($x=0;$x<imagesx($i);$x++) {
 for ($y=0;$y<imagesy($i);$y++) {
 $rgb = imagecolorat($i,$x,$y);
 $r = ($rgb >> 16) & 0xFF;
 $g = ($rgb >> & 0xFF;
 $b = $rgb & 0xFF;
 $rTotal += $r;
 $gTotal += $g;
 $bTotal += $b;
 $total++;
 }
}
$rAverage = round($rTotal/$total);
$gAverage = round($gTotal/$total);
$bAverage = round($bTotal/$total);
}
8. whois query
Using the below function you would be able to get complete details regarding the owner of any domain.
function whois_query($domain) {
 // fix the domain name:
 $domain = strtolower(trim($domain));
 $domain = preg_replace('/^http:\/\//i', '', $domain);
 $domain = preg_replace('/^www\./i', '', $domain);
 $domain = explode('/', $domain);
 $domain = trim($domain[0]);
 // split the TLD from domain name
 $_domain = explode('.', $domain);
 8 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 $lst = count($_domain)-1;
 $ext = $_domain[$lst];
 // You find resources and lists
 // like these on wikipedia:
 //
 // http://de.wikipedia.org/wiki/Whois
 //
 $servers = array(
 "biz" => "whois.neulevel.biz",
 "com" => "whois.internic.net",
 "us" => "whois.nic.us",
 "coop" => "whois.nic.coop",
 "info" => "whois.nic.info",
 "name" => "whois.nic.name",
 "net" => "whois.internic.net",
 "gov" => "whois.nic.gov",
 "edu" => "whois.internic.net",
 "mil" => "rs.internic.net",
 "int" => "whois.iana.org",
 "ac" => "whois.nic.ac",
 "ae" => "whois.uaenic.ae",
 "at" => "whois.ripe.net",
 "au" => "whois.aunic.net",
 "be" => "whois.dns.be",
 "bg" => "whois.ripe.net",
 "br" => "whois.registro.br",
 "bz" => "whois.belizenic.bz",
 "ca" => "whois.cira.ca",
 "cc" => "whois.nic.cc",
 "ch" => "whois.nic.ch",
 "cl" => "whois.nic.cl",
 "cn" => "whois.cnnic.net.cn",
 "cz" => "whois.nic.cz",
 "de" => "whois.nic.de",
 "fr" => "whois.nic.fr",
 "hu" => "whois.nic.hu",
 "ie" => "whois.domainregistry.ie",
 "il" => "whois.isoc.org.il",
 "in" => "whois.ncst.ernet.in",
 "ir" => "whois.nic.ir",
 "mc" => "whois.ripe.net",
 "to" => "whois.tonic.to",
 "tv" => "whois.tv",
 "ru" => "whois.ripn.net",
 "org" => "whois.pir.org",
 9 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 "aero" => "whois.information.aero",
 "nl" => "whois.domain-registry.nl"
 );
 if (!isset($servers[$ext])){
 die('Error: No matching nic server found!');
 }
 $nic_server = $servers[$ext];
 $output = '';
 // connect to whois server:
 if ($conn = fsockopen ($nic_server, 43)) {
 fputs($conn, $domain."\r\n");
 while(!feof($conn)) {
 $output .= fgets($conn,128);
 }
 fclose($conn);
 }
 else { die('Error: Could not connect to ' . $nic_server . '!'); }
 return $output;
}
Syntax
<?php
$domain = "http://www.blog.koonk.com";
$result = whois_query($domain);
print_r($result);
?>
9. validate email address
Sometimes, while filling forms on your website, a user can mistype his email address. Using the below
function you can check if the email provided by user is in correct format.
function is_validemail($email)
{
$check = 0;
if(filter_var($email,FILTER_VALIDATE_EMAIL))
 10 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
{
$check = 1;
}
return $check;
}
Syntax
<?php
$email = "blog@koonk.com";
$check = is_validemail($email);
echo $check;
// If the output is 1, then email is valid.
?>
10. get real ip address of user
function getRealIpAddr()
{
 if (!emptyempty($_SERVER['HTTP_CLIENT_IP']))
 {
 $ip=$_SERVER['HTTP_CLIENT_IP'];
 }
 elseif (!emptyempty($_SERVER['HTTP_X_FORWARDED_FOR']))
 //to check ip is pass from proxy
 {
 $ip=$_SERVER['HTTP_X_FORWARDED_FOR'];
 }
 else
 {
 $ip=$_SERVER['REMOTE_ADDR'];
 }
 return $ip;
}
Syntax
<?php
$ip = getRealIpAddr();
echo $ip;
 11 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
?>
11. Convert url in a string to hyperlinks
If you are working with forums, blogs or even a normal form submission, many times you end us asking
user for a website. Using the function, the URL string would automatically convert to a hyperlink.
function makeClickableLinks($text)
{
 $text = eregi_replace('(((f|ht){1}tp://)[-a-zAZ0-9@:%_+.~#?&//=]+)',

 '<a href="\1">\1</a>', $text);
 $text = eregi_replace('([[:space:]()[{}])(www.[-a-zAZ0-9@:%_+.~#?&//=]+)',

 '\1<a href="http://\2">\2</a>', $text);
 $text = eregi_replace('([_.0-9a-z-]+@([0-9a-z][0-9a-z-]+.)+[az]{2,3})',

 '<a href="mailto:\1">\1</a>', $text);

return $text;
}
Syntax
<?php
$text = "This is my first post on http://blog.koonk.com";
$text = makeClickableLinks($text);
echo $text;
?>
12. block multiple ip from accessing your website
This snippet can come in handy if you want to restrict your website from a certain location or from
certain users.
if ( !file_exists('blocked_ips.txt') ) {
 $deny_ips = array(
 '127.0.0.1',
 '192.168.1.1',
 '83.76.27.9',
 12 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 '192.168.1.163'
 );
} else {
 $deny_ips = file('blocked_ips.txt');
}
// read user ip adress:
$ip = isset($_SERVER['REMOTE_ADDR']) ? trim($_SERVER['REMOTE_ADDR']) :
 '';
// search current IP in $deny_ips array
if ( (array_search($ip, $deny_ips))!== FALSE ) {
 // address is blocked:
 echo 'Your IP adress ('.$ip.') was blocked!';
 exit;
}
13. forceful downloading of file
If you want certain files to download instead of opening in new tab, you can use the below snippet.
function force_download($file)
{
 $dir = "../log/exports/";
 if ((isset($file))&&(file_exists($dir.$file))) {
 header("Content-type: application/force-download");
 header('ContentDisposition:
inline; filename="' . $dir.$file . '"');
 header("Content-Transfer-Encoding: Binary");
 header("Content-length: ".filesize($dir.$file));
 header('Content-Type: application/octet-stream');
 header('ContentDisposition:
attachment; filename="' . $file . '"');
 readfile("$dir$file");
 } else {
 echo "No file selected";
 }
}
Syntax
 13 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
<php
force_download("image.jpg");
?>
14. CREATING json data
Using below PHP snippet you can create JSON data. This will come handy when you are creating web
services for mobile apps.
$json_data = array ('id'=>1,'name'=>"Mohit");
echo json_encode($json_data);
15. zipping a file
Using the below PHP snippet you would be able to zip files instantly.
function create_zip($files = array(),$destination = '',$overwrite = fa
lse) {
 //if the zip file already exists and overwrite is false, return fa
lse
 if(file_exists($destination) && !$overwrite) { return false; }
 //vars
 $valid_files = array();
 //if files were passed in...
 if(is_array($files)) {
 //cycle through each file
 foreach($files as $file) {
 //make sure the file exists
 if(file_exists($file)) {
 $valid_files[] = $file;
 }
 }
 }
 //if we have good files...
 if(count($valid_files)) {
 //create the archive
 $zip = new ZipArchive();
 if($zip->open($destination,$overwrite ? ZIPARCHIVE::OVERWRITE
: ZIPARCHIVE::CREATE) !== true) {
 return false;
 }
 //add the files
 14 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 foreach($valid_files as $file) {
 $zip->addFile($file,$file);
 }
 //debug
 //echo 'The zip archive contains ',$zip->numFiles,' files with
 a status of ',$zip->status;

 //close the zip -- done!
 $zip->close();

 //check to make sure the file exists
 return file_exists($destination);
 }
 else
 {
 return false;
 }
}
Syntax
<?php
$files=array('file1.jpg', 'file2.jpg', 'file3.gif');
create_zip($files, 'myzipfile.zip', true);
?>
16. unzipping a file
Using the below PHP snippet you would be able to unzip a file on the fly.
function unzip($location,$newLocation)
{
 if(exec("unzip $location",$arr)){
 mkdir($newLocation);
 for($i = 1;$i< count($arr);$i++){
 $file = trim(preg_replace("~inflating: ~","",$arr[$i])
);
 copy($location.'/'.$file,$newLocation.'/'.$file);
 unlink($location.'/'.$file);
 }
 return TRUE;
 }else{
 15 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 return FALSE;
 }
}
Syntax
<?php
unzip('test.zip','unziped/test'); //File would be unzipped in unziped/
test folder
?>
17. php snippet to resize image
Sometimes client can upload a large image and you might want to resize it. Below PHP snippet would
enable you to do that.
function resize_image($filename, $tmpname, $xmax, $ymax)
{
 $ext = explode(".", $filename);
 $ext = $ext[count($ext)-1];

 if($ext == "jpg" || $ext == "jpeg")
 $im = imagecreatefromjpeg($tmpname);
 elseif($ext == "png")
 $im = imagecreatefrompng($tmpname);
 elseif($ext == "gif")
 $im = imagecreatefromgif($tmpname);

 $x = imagesx($im);
 $y = imagesy($im);

 if($x <= $xmax && $y <= $ymax)
 return $im;

 if($x >= $y) {
 $newx = $xmax;
 $newy = $newx * $y / $x;
 }
 else {
 $newy = $ymax;
 $newx = $x / $y * $newy;
 }
 16 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com

 $im2 = imagecreatetruecolor($newx, $newy);
 imagecopyresized($im2, $im, 0, 0, 0, 0, floor($newx), floor($newy)
, $x, $y);
 return $im2;
}
18. send email using mail()
Above we have discussed how you can send email using Mandrill, but in case you do not want to use a
third party service, you can use the below snippet.
function send_mail($to,$subject,$body)
{
$headers = "From: KOONK\r\n";
$headers .= "Reply-To: blog@koonk.com\r\n";
$headers .= "Return-Path: blog@koonk.com\r\n";
$headers .= "X-Mailer: PHP5\n";
$headers .= 'MIME-Version: 1.0' . "\n";
$headers .= 'Content-type: text/html; charset=iso-8859-1' . "\r\n";
mail($to,$subject,$body,$headers);
}
Syntax
<?php
$to = "admin@koonk.com";
$subject = "This is a test mail";
$body = "Hello World!";
send_mail($to,$subject,$body);
?>
19. CONVERT seconds to days, hours and minutes
function secsToStr($secs) {
 if($secs>=86400){$days=floor($secs/86400);$secs=$secs%86400;$r=$da
ys.' day';if($days<>1){$r.='s';}if($secs>0){$r.=', ';}}
 if($secs>=3600){$hours=floor($secs/3600);$secs=$secs%3600;$r.=$hou
rs.' hour';if($hours<>1){$r.='s';}if($secs>0){$r.=', ';}}
 17 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 if($secs>=60){$minutes=floor($secs/60);$secs=$secs%60;$r.=$minutes
.' minute';if($minutes<>1){$r.='s';}if($secs>0){$r.=', ';}}
 $r.=$secs.' second';if($secs<>1){$r.='s';}
 return $r;
}
Syntax
<?php
$seconds = "56789";
$output = secsToStr($seconds);
echo $output;
?>
20. php code snippet for database connection
To store data, you would need a database. In this case we would be using MySQL database.
<?php
$DBNAME = 'koonk';
$HOST = 'localhost';
$DBUSER = 'root';
$DBPASS = 'koonk';
$CONNECT = mysql_connect($HOST,$DBUSER,$DBPASS);
if(!$CONNECT)
{
 echo 'MySQL Error: '.mysql_error();
}
$SELECT = mysql_select_db($DBNAME);
if(!$SELECT)
{
 echo 'MySQL Error: '.mysql_error();
}
?>
In the highlighted lines above, you would have to specify your own server database details.
You can save the above file as config.php and you would have to include that file in all pages where you
need database connectivity.
 18 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
21. php snippet for directory listing
Using the below PHP code snippet you would be able to list all files and folders in a directory.
function list_files($dir)
{
 if(is_dir($dir))
 {
 if($handle = opendir($dir))
 {
 while(($file = readdir($handle)) !== false)
 {
 if($file != "." &amp;&amp; $file != ".." &amp;&amp; $f
ile != "Thumbs.db"/*pesky windows, images..*/)
 {
 echo '<a target="_blank" href="'.$dir.$file.'">'.$
file.'</a><br>'."\n";
 }
 }
 closedir($handle);
 }
 }
}
Syntax
<?php
 list_files("images/"); //This will list all files of images folder
?>
22. detect USER language
Using the below PHP snippet you would be able to detect the language of your user's browser.
function get_client_language($availableLanguages, $default='en'){
 if (isset($_SERVER['HTTP_ACCEPT_LANGUAGE'])) {
 $langs=explode(',',$_SERVER['HTTP_ACCEPT_LANGUAGE']);
 foreach ($langs as $value){
 $choice=substr($value,0,2);
 if(in_array($choice, $availableLanguages)){
 19 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 return $choice;
 }
 }
 }
 return $default;
}
23. reading a csv file
function readCSV($csvFile){
 $file_handle = fopen($csvFile, 'r');
 while (!feof($file_handle) ) {
 $line_of_text[] = fgetcsv($file_handle, 1024);
 }
 fclose($file_handle);
 return $line_of_text;
}
Syntax
<?php
$csvFile = "test.csv";
$csv = readCSV($csvFile);
$a = csv[0][0]; // This will get value of Column 1 & Row 1
?>
24. creating a csv file from php array
function generateCsv($data, $delimiter = ',', $enclosure = '"') {
 $handle = fopen('php://temp', 'r+');
 foreach ($data as $line) {
 fputcsv($handle, $line, $delimiter, $enclosure);
 }
 rewind($handle);
 while (!feof($handle)) {
 $contents .= fread($handle, 8192);
 }
 fclose($handle);
 return $contents;
}
 20 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
Syntax
<?php
$data[0] = "apple";
$data[1] = "oranges";
generateCsv($data, $delimiter = ',', $enclosure = '"');
?>
25. parsing xml data
Below PHP snippet will help you explain how you can parse XML data in PHP.
$xml_string="<?xml version='1.0'?>
<moleculedb>
 <molecule name='Benzine'>
 <symbol>ben</symbol>
 <code>A</code>
 </molecule>
 <molecule name='Water'>
 <symbol>h2o</symbol>
 <code>K</code>
 </molecule>
</moleculedb>";
//load the xml string using simplexml function
$xml = simplexml_load_string($xml_string);
//loop through the each node of molecule
foreach ($xml->molecule as $record)
{
 //attribute are accessted by
 echo $record['name'], ' ';
 //node are accessted by -> operator
 echo $record->symbol, ' ';
 echo $record->code, '<br />';
}
26. PARSING JSON DATA
$json_string='{"id":1,"name":"rolf","country":"russia","office":["goog
 21 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
le","oracle"]} ';
$obj=json_decode($json_string);
//print the parsed data
echo $obj->name; //displays rolf
echo $obj->office[0]; //displays google
27. get current page url
This PHP snippet comes handy when post login you have to redirect user to the same page which he was
browsing earlier.
function current_url()
{
$url = "http://" . $_SERVER['HTTP_HOST'] . $_SERVER['REQUEST_URI'];
$validURL = str_replace("&", "&amp;", $url);
return validURL;
}
Syntax
<?php
echo "Currently you are on: ".current_url();
?>
28. get latest tweet from ANY twitter account
function my_twitter($username)
{
 $no_of_tweets = 1;
 $feed = "http://search.twitter.com/search.atom?q=from:" . $username
. "&rpp=" . $no_of_tweets;
 $xml = simplexml_load_file($feed);
 foreach($xml->children() as $child) {
 foreach ($child as $value) {
 if($value->getName() == "link") $link = $value['href'];
 if($value->getName() == "content") {
 $content = $value . "";
 echo '<p class="twit">'.$content.' <a class="twt" href="'.$link.'" t
itle="">&nbsp; </a></p>';
 22 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 }
 }
 }
}
Syntax
<?php
$handle = "koonktech";
my_twitter($handle);
?>
29. number of retweets
Using this PHP snippet you can check the number of times your page URL was retweeted.
function tweetCount($url) {
 $content = file_get_contents("http://api.tweetmeme.com/url_info?ur
l=".$url);
 $element = new SimpleXmlElement($content);
 $retweets = $element->story->url_count;
 if($retweets){
 return $retweets;
 } else {
 return 0;
 }
}
Syntax
<?php
$url = "http://blog.koonk.com";
$count = tweetCount($url);
return $count;
?>
30. calculate difference in 2 dates
 23 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
<?php
$date1 = date( 'Y-m-d' );
$date2 = "2015-12-04";
$diff = abs(strtotime($date2) - strtotime($date1));
$years = floor($diff / (365*60*60*24));
$months = floor(($diff - $years * 365*60*60*24) / (30*60*60*24));
$days = floor(($diff - $years * 365*60*60*24 - $months*30*60*60*24)/ (
60*60*24));
printf("%d years, %d months, %d days\n", $years, $months, $days);
-------------------------------------------------------- OR
$date1 = new DateTime("2007-03-24");
$date2 = new DateTime("2009-06-26");
$interval = $date1->diff($date2);
echo "difference " . $interval->y . " years, " . $interval->m." months
, ".$interval->d." days ";
// shows the total amount of days (not divided into years, months and
days like above)
echo "difference " . $interval->days . " days ";
-------------------------------------------------------- OR


/**
 * Calculate differences between two dates with precise semantics. Bas
ed on PHPs DateTime::diff()
 * implementation by Derick Rethans. Ported to PHP by Emil H, 2011-05-
02. No rights reserved.
 *
 * See here for original code:
 * http://svn.php.net/viewvc/php/phpsrc/trunk/ext/date/lib/tm2unixtime.c?revision=302890&view=markup
 * http://svn.php.net/viewvc/php/phpsrc/trunk/ext/date/lib/interval.c?revision=298973&view=markup
 */
function _date_range_limit($start, $end, $adj, $a, $b, $result)
{
 24 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 if ($result[$a] < $start) {
 $result[$b] -= intval(($start - $result[$a] - 1) / $adj) + 1;
 $result[$a] += $adj * intval(($start - $result[$a] - 1) / $adj
 + 1);
 }
 if ($result[$a] >= $end) {
 $result[$b] += intval($result[$a] / $adj);
 $result[$a] -= $adj * intval($result[$a] / $adj);
 }
 return $result;
}
function _date_range_limit_days($base, $result)
{
 $days_in_month_leap = array(31, 31, 29, 31, 30, 31, 30, 31, 31, 30
, 31, 30, 31);
 $days_in_month = array(31, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31,
 30, 31);
 _date_range_limit(1, 13, 12, "m", "y", &$base);
 $year = $base["y"];
 $month = $base["m"];
 if (!$result["invert"]) {
 while ($result["d"] < 0) {
 $month--;
 if ($month < 1) {
 $month += 12;
 $year--;
 }
 $leapyear = $year % 400 == 0 || ($year % 100 != 0 && $year
 % 4 == 0);
 $days = $leapyear ? $days_in_month_leap[$month] : $days_in
_month[$month];
 $result["d"] += $days;
 $result["m"]--;
 }
 } else {
 while ($result["d"] < 0) {
 $leapyear = $year % 400 == 0 || ($year % 100 != 0 && $year
 % 4 == 0);
 25 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 $days = $leapyear ? $days_in_month_leap[$month] : $days_in
_month[$month];
 $result["d"] += $days;
 $result["m"]--;
 $month++;
 if ($month > 12) {
 $month -= 12;
 $year++;
 }
 }
 }
 return $result;
}
function _date_normalize($base, $result)
{
 $result = _date_range_limit(0, 60, 60, "s", "i", $result);
 $result = _date_range_limit(0, 60, 60, "i", "h", $result);
 $result = _date_range_limit(0, 24, 24, "h", "d", $result);
 $result = _date_range_limit(0, 12, 12, "m", "y", $result);
 $result = _date_range_limit_days(&$base, &$result);
 $result = _date_range_limit(0, 12, 12, "m", "y", $result);
 return $result;
}
/**
 * Accepts two unix timestamps.
 */
function _date_diff($one, $two)
{
 $invert = false;
 if ($one > $two) {
 list($one, $two) = array($two, $one);
 $invert = true;
 }
 $key = array("y", "m", "d", "h", "i", "s");
 $a = array_combine($key, array_map("intval", explode(" ", date("Y
m d H i s", $one))));
 $b = array_combine($key, array_map("intval", explode(" ", date("Y
 26 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
m d H i s", $two))));
 $result = array();
 $result["y"] = $b["y"] - $a["y"];
 $result["m"] = $b["m"] - $a["m"];
 $result["d"] = $b["d"] - $a["d"];
 $result["h"] = $b["h"] - $a["h"];
 $result["i"] = $b["i"] - $a["i"];
 $result["s"] = $b["s"] - $a["s"];
 $result["invert"] = $invert ? 1 : 0;
 $result["days"] = intval(abs(($one - $two)/86400));
 if ($invert) {
 _date_normalize(&$a, &$result);
 } else {
 _date_normalize(&$b, &$result);
 }
 return $result;
}
$date = "2014-12-04 19:37:22";
echo '<pre>';
print_r( _date_diff( strtotime($date), time() ) );
echo '</pre>';
?>
31. php snippet to delete a folder with content
function Delete($path)
{
 if (is_dir($path) === true)
 {
 $files = array_diff(scandir($path), array('.', '..'));
 foreach ($files as $file)
 {
 Delete(realpath($path) . '/' . $file);
 }
 return rmdir($path);
 }
 27 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 else if (is_file($path) === true)
 {
 return unlink($path);
 }
 return false;
}
Syntax
<?php
$path = "images/";
Delete($path); // This will delete images folder along with its conten
ts.
?>
32. search and highlight keywords in a string
It becomes convenient for user, when he searches something and in the result he can see his keyword
highlighted.
function highlighter_text($text, $words)
{
 $split_words = explode( " " , $words );
 foreach($split_words as $word)
 {
 $color = "#4285F4";
 $text = preg_replace("|($word)|Ui" ,
 "<span style=\"color:".$color.";\"><b>$1</b></span>" , $te
xt );
 }
 return $text;
}
Syntax
<?php
$string = "I like chocolates and I like apples";
$words = "apple";
echo highlighter_text($string ,$words);
 28 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
?>
33. php snippet to write to a file
<?
$filename = 'blog.csv';
$fp = fopen($filename, 'w');
$output = " Hello ";
$output .= " World! ";
$output .= "\r\n";
fputs($fp, $output);
fclose($fp);
?>
34. download image from url
Most of the websites now give you an option to either upload image or download it from a URL. Ever
wondered how they copy that image from that URL? Here is how
function imagefromURL($image,$rename)
{
$ch = curl_init($image);
curl_setopt($ch, CURLOPT_HEADER, 0);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_BINARYTRANSFER,1);
$rawdata=curl_exec ($ch);
curl_close ($ch);
$fp = fopen("$rename",'w');
fwrite($fp, $rawdata);
fclose($fp);
}
Syntax
<?php
$url = "http://koonk.com/images/logo.png";
$rename = "koonk.png";
imagefromURL($url,$rename);
 29 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
?>
35. check if a url is valid
function isvalidURL($url)
{
$check = 0;
if (filter_var($url, FILTER_VALIDATE_URL) !== false) {
 $check = 1;
}
return $check;
}
Syntax
<?php
$url = "http://koonk.com";
$check = checkvalidURL($url);
echo $check; //if returns 1 then URL is valid.
?>
36. generate qr code
function qr_code($data, $type = "TXT", $size ='150', $ec='L', $margin=
'0')
{
 $types = array("URL" =--> "http://", "TEL" => "TEL:", "TXT"=>"",
"EMAIL" => "MAILTO:");
 if(!in_array($type,array("URL", "TEL", "TXT", "EMAIL")))
 {
 $type = "TXT";
 }
 if (!preg_match('/^'.$types[$type].'/', $data))
 {
 $data = str_replace("\\", "", $types[$type]).$data;
 }
 $ch = curl_init();
 $data = urlencode($data);
 curl_setopt($ch, CURLOPT_URL, 'http://chart.apis.google.com/chart'
);
 30 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 curl_setopt($ch, CURLOPT_POST, true);
 curl_setopt($ch, CURLOPT_POSTFIELDS, 'chs='.$size.'x'.$size.'&cht=
qr&chld='.$ec.'|'.$margin.'&chl='.$data);
 curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
 curl_setopt($ch, CURLOPT_HEADER, false);
 curl_setopt($ch, CURLOPT_TIMEOUT, 30);
 $response = curl_exec($ch);
 curl_close($ch);
 return $response;
}
Syntax
<?php
header("Content-type: image/png");
echo qr_code("http://koonk.com", "URL");
?>
37. calculate distance between two map coordinates
function getDistanceBetweenPointsNew($latitude1, $longitude1, $latitud
e2, $longitude2) {
 $theta = $longitude1 - $longitude2;
 $miles = (sin(deg2rad($latitude1)) * sin(deg2rad($latitude2))) + (
cos(deg2rad($latitude1)) * cos(deg2rad($latitude2)) * cos(deg2rad($the
ta)));
 $miles = acos($miles);
 $miles = rad2deg($miles);
 $miles = $miles * 60 * 1.1515;
 $feet = $miles * 5280;
 $yards = $feet / 3;
 $kilometers = $miles * 1.609344;
 $meters = $kilometers * 1000;
 return compact('miles','feet','yards','kilometers','meters');
}
Syntax
 31 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
<?php
$point1 = array('lat' => 40.770623, 'long' => -73.964367);
$point2 = array('lat' => 40.758224, 'long' => -73.917404);
$distance = getDistanceBetweenPointsNew($point1['lat'], $point1['long'
], $point2['lat'], $point2['long']);
foreach ($distance as $unit => $value) {
 echo $unit.': '.number_format($value,4).'<br />';
}
?>
38. get all tweets of a specific hashtag
function getTweets($hash_tag) {
 $url = 'http://search.twitter.com/search.atom?q='.urlencode($hash_
tag) ;
 echo "<p>Connecting to <strong>$url</strong> ...</p>";
 $ch = curl_init($url);
 curl_setopt ($ch, CURLOPT_RETURNTRANSFER, TRUE);
 $xml = curl_exec ($ch);
 curl_close ($ch);
 //If you want to see the response from Twitter, uncomment this nex
t part out:
 //echo "<p>Response:</p>";
 //echo "<pre>".htmlspecialchars($xml)."</pre>";
 $affected = 0;
 $twelement = new SimpleXMLElement($xml);
 foreach ($twelement->entry as $entry) {
 $text = trim($entry->title);
 $author = trim($entry->author->name);
 $time = strtotime($entry->published);
 $id = $entry->id;
 echo "<p>Tweet from ".$author.": <strong>".$text."</strong> <
em>Posted ".date('n/j/y g:i a',$time)."</em></p>";
 }
 return true ;
}
39. Add th,st,nd or rd to the end of a number
 32 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
Friday the 13th
function ordinal($cdnl){
 $test_c = abs($cdnl) % 10;
 $ext = ((abs($cdnl) %100 < 21 && abs($cdnl) %100 > 4) ? 'th'
 : (($test_c < 4) ? ($test_c < 3) ? ($test_c < 2) ? ($test_
c < 1)
 ? 'th' : 'st' : 'nd' : 'rd' : 'th'));
 return $cdnl.$ext;
}
Syntax
<?php
$number = 10;
echo ordinal($number); //output is 10th
?>
40. file download with speed limit
If you are planning to create a file sharing website then you would already know that bandwidth is the
key. Here is how you can limit the speed at which the file will download.
<?php
// local file that should be send to the client
$local_file = 'test-file.zip';
// filename that the user gets as default
$download_file = 'your-download-name.zip';
// set the download rate limit (=> 20,5 kb/s)
$download_rate = 20.5;
if(file_exists($local_file) && is_file($local_file)) {
 // send headers
 header('Cache-control: private');
 header('Content-Type: application/octet-stream');
 header('Content-Length: '.filesize($local_file));
 header('Content-Disposition: filename='.$download_file);
 // flush content
 flush();
 // open file stream
 33 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 $file = fopen($local_file, "r");
 while(!feof($file)) {
 // send the current file part to the browser
 print fread($file, round($download_rate * 1024));
 // flush the content to the browser
 flush();
 // sleep one second
 sleep(1);
 }
 // close file stream
 fclose($file);}
else {
 die('Error: The file '.$local_file.' does not exist!');
}
?>
41. convert text to image
<?php
header("Content-type: image/png");
$string = $_GET['text'];
$im = imagecreatefrompng("images/button.png");
$color = imagecolorallocate($im, 255, 255, 255);
$px = (imagesx($im) - 7.5 * strlen($string)) / 2;
$py = 9;
$fontSize = 1;
imagestring($im, fontSize, $px, $py, $string, $color);
imagepng($im);
imagedestroy($im);
?>
42. get remote file size
function remote_filesize($url, $user = "", $pw = "")
{
 ob_start();
 $ch = curl_init($url);
 34 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 curl_setopt($ch, CURLOPT_HEADER, 1);
 curl_setopt($ch, CURLOPT_NOBODY, 1);
 if(!empty($user) && !empty($pw))
 {
 $headers = array('Authorization: Basic ' . base64_encode("$user:$pw
"));
 curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
 }
 $ok = curl_exec($ch);
 curl_close($ch);
 $head = ob_get_contents();
 ob_end_clean();
 $regex = '/Content-Length:\s([0-9].+?)\s/';
 $count = preg_match($regex, $head, $matches);
 return isset($matches[1]) ? $matches[1] : "unknown";
}
Syntax
<?php
$file = "http://koonk.com/images/logo.png";
$size = remote_filesize($url);
echo $size;
?>
43. pdf to image converter with imagebrick
<?php
$pdf_file = './pdf/demo.pdf';
$save_to = './jpg/demo.jpg'; //make sure that apache has permis
sions to write in this folder! (common problem)
//execute ImageMagick command 'convert' and convert PDF to JPG with ap
plied settings
exec('convert "'.$pdf_file.'" -colorspace RGB -resize 800 "'.$save_to.
'"', $output, $return_var);
 35 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
if($return_var == 0) { //if exec successfuly converted pd
f to jpg
 print "Conversion OK";
}
else print "Conversion failed.<br />".$output;
?>
44. URL SHORTENER using tinyurl
function get_tiny_url($url)
{
 $ch = curl_init();
 $timeout = 5;
 curl_setopt($ch,CURLOPT_URL,'http://tinyurl.com/apicreate.php?url='.$url);

 curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
 curl_setopt($ch,CURLOPT_CONNECTTIMEOUT,$timeout);
 $data = curl_exec($ch);
 curl_close($ch);
 return $data;
}
Syntax
<?php
$url = "http://blog.koonk.com/2015/07/Hello-World";
$tinyurl = get_tiny_url($url);
echo $tinyurl;
?>
45. youtube download link generator
Using the below PHP snippet you can offer your users the ability to download youtube videos.
function str_between($string, $start, $end)
{
$string = " ".$string; $ini = strpos($string,$start); if ($ini == 0) r
eturn ""; $ini += strlen($start); $len = strpos($string,$end,$ini) - $
ini; return substr($string,$ini,$len); }
 36 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
function get_youtube_download_link(){
 $youtube_link = $_GET['youtube'];
 $youtube_page = file_get_contents($youtube_link);
 $v_id = str_between($youtube_page, "&video_id=", "&");
 $t_id = str_between($youtube_page, "&t=", "&");
 $flv_link = "http://www.youtube.com/get_video?video_id=$v_id&t=$t_id"
;
 $hq_flv_link = "http://www.youtube.com/get_video?video_id=$v_id&t=$t_
id&fmt=6";
 $mp4_link = "http://www.youtube.com/get_video?video_id=$v_id&t=$t_id&
fmt=18";
 $threegp_link = "http://www.youtube.com/get_video?video_id=$v_id&t=$t
_id&fmt=17";
 echo "\t\tDownload (right-click &gt; save as)&#58;\n\t\t";
 echo "<a href=\"$flv_link\">FLV</a>\n\t\t";
 echo "<a href=\"$hq_flv_link\">HQ FLV (if available)</a>\n\t\t";
 echo "<a href=\"$mp4_link\">MP4</a>\n\t\t";
 echo "<a href=\"$threegp_link\">3GP</a><br><br>\n";
}
46. Facebook style timestamp
Ever noticed how your post/comment time is shown on Facebook (x mins age, y hours ago etc). Below is
a simple PHP snippet which you can use that will do the same thing.
function nicetime($date)
{
 if(empty($date)) {
 return "No date provided";
 }

 $periods = array("second", "minute", "hour", "day", "week"
, "month", "year", "decade");
 $lengths = array("60","60","24","7","4.35","12","10");

 $now = time();
 $unix_date = strtotime($date);

 // check validity of date
 if(empty($unix_date)) {
 return "Bad date";
 }
 // is it future date or past date
 37 / 39
46 useful PHP code Snippets that can help you with your PHP projects - 07-06-2015
by Mohit Madan - KOONK Technologies - http://blog.koonk.com
 if($now > $unix_date) {
 $difference = $now - $unix_date;
 $tense = "ago";

 } else {
 $difference = $unix_date - $now;
 $tense = "from now";
 }

 for($j = 0; $difference >= $lengths[$j] && $j < count($lengths)-1;
 $j++) {
 $difference /= $lengths[$j];
 }

 $difference = round($difference);

 if($difference != 1) {
 $periods[$j].= "s";
 }

 return "$difference $periods[$j] {$tense}";
}
Syntax
<?php
$date = "2015-07-05 03:45";
$result = nicetime($date); // 2 days ago
?>
