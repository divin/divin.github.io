---
title: Contact
permalink: /contact
layout: default
---

<link rel = "stylesheet" property = "stylesheet" href = "/assets/css/contact.css">

<form action = "https://formspree.io/f/moqpnqdv" accept-charset = "utf-8" method = "POST">
<fieldset>
<label for = "name">Your Name:</label>
<input type = "text" name = "name" id = "name" placeholder = "Tell me your name" required>

<label for = "email">Your E-Mail:</label>
<input type = "email" name = "_replyto" id = "email" placeholder = "Your e-mail" required>

<label for = "message">Your Message:</label>
<textarea rows = "5" name = "message" id = "message" placeholder = "Type your message here" required></textarea>
<input type = "hidden" name = "_subject" id = "email-subject" value = "Contact Form Submission">
<button type = "submit">Submit</button>
</fieldset>
</form>