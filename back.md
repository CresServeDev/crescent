<!-- <?php

    // Only process POST reqeusts.

    if ($_SERVER["REQUEST_METHOD"] == "POST") {

        // Get the form fields and remove MORALspace.

        $name = strip_tags(trim($_POST["name"]));

				$name = str_replace(array("\r","\n"),array(" "," "),$name);

        $email = filter_var(trim($_POST["email"]), FILTER_SANITIZE_EMAIL);

        $website = trim($_POST["website"]);

        $number = trim($_POST["number"]);

        $message = trim($_POST["message"]);



        // Check that data was sent to the mailer.

        if ( empty($name)or empty($phone) OR empty($subject) OR empty($message) OR !filter_var($email, FILTER_VALIDATE_EMAIL)) {

            // Set a 400 (bad request) response code and exit.

            http_response_code(400);

            echo "Please complete the form and try again.";

            exit;

        }



        // Set the recipient email address.

        // FIXME: Update this to your desired email address.

        $recipient = "testhub582@gmail.com";



        // Set the email subject.

        $sender = "New contact from $name";



        //Email Header

        $head = " /// Johanspond \\\ ";



        // Build the email content.

        $email_content = "$head\n\n\n";

        $email_content .= "Name: $name\n";

        $email_content .= "Email: $email\n\n";

        $email_content .= "Number: $number\n\n";

        $email_content .= "Website: $website\n\n";

        $email_content .= "Message:\n$message\n";



        // Build the email headers.

        $email_headers = "From: $name <$email>";



        // Send the email.

        if (mail($recipient, $sender, $email_content, $email_headers)) {

            // Set a 200 (okay) response code.

            http_response_code(200);

            echo "Thank You! Your message has been sent.";

        } else {

            // Set a 500 (internal server error) response code.

            http_response_code(500);

            echo "Oops! Something went wrong and we couldn't send your message.";

        }



    } else {

        // Not a POST request, set a 403 (forbidden) response code.

        http_response_code(403);

        echo "There was a problem with your submission, please try again.";

    }



?>
 -->


<?php
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\Exception;

require '../PHPMailer/src/Exception.php';
require '../PHPMailer/src/PHPMailer.php';
require '../PHPMailer/src/SMTP.php';

if ($_SERVER["REQUEST_METHOD"] !== "POST") {
    exit("Invalid request");
}

// -------- SANITIZE --------
$fname   = htmlspecialchars($_POST['fname'] ?? '');
$lname   = htmlspecialchars($_POST['lname'] ?? '');
$email   = filter_var($_POST['email'] ?? '', FILTER_VALIDATE_EMAIL);
$number  = htmlspecialchars($_POST['number'] ?? '');
$message = htmlspecialchars($_POST['messages'] ?? '');

if (!$fname || !$email || !$number) {
    exit("Required fields missing");
}

// -------- MAIL --------
$mail = new PHPMailer(true);

try {
    $mail->isSMTP();
    $mail->Host       = 'smtp.gmail.com';
    $mail->SMTPAuth   = true;
    $mail->Username   = 'contact.crescenttechno@gmail.com';      // 🔴 change
    $mail->Password   = 'vtdotbcohduazfpw';        // 🔴 Gmail App Password
    $mail->SMTPSecure = PHPMailer::ENCRYPTION_STARTTLS;
    $mail->Port       = 587;

    $mail->setFrom($email, $fname);
    $mail->addReplyTo($email, $fname);
    $mail->addAddress('support@crescenttechnoserve.com');       // 🔴 receive here

    $mail->isHTML(true);
    $mail->Subject = "New Contact Message from $fname";
    $mail->Body    = "
        <h3>New Contact Message</h3>
        <p><strong>Name:</strong> $fname $lname</p>
        <p><strong>Email:</strong> $email</p>
        <p><strong>Phone:</strong> $number</p>
        <p><strong>Message:</strong><br>$message</p>
    ";

    $mail->send();
    echo "success";

} catch (Exception $e) {
    echo "Mailer Error: {$mail->ErrorInfo}";
}