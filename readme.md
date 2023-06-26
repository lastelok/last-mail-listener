# Overview

Mail-listener library for node.js. Get notification when new email arrived to inbox or when message metadata (e.g. flags) changes externally. Uses IMAP protocol.

## Use

Install

`npm install last-mail-listener`

## Version Notes

Change notes:

-   v1.1.1:
    -   Updated dependencies (Works now with NODEJS v18.16.0 LTS).

NOTE: This version is designed to work with & tested on NodeJS v 18.16.0 LTS, the most recent LTS version as at June 2023. It might not work on older versions of Node.

JavaScript Code:

```javascript

var { MailListener } = require("last-mail-listener");   // NOTE: A FUTURE VERSION (release date TBA) will not require ES6 destructuring or referring to the class after the require statement (i.e. require('mail-listener5').MailListener). At this stage, this is necessary because index.js exports the MailListener class as a property of module.exports.

var mailListener = new MailListener({
  username: "imap-username",
  password: "imap-password",
  host: "imap-host",
  port: 993, // imap port
  tls: true,
  connTimeout: 10000, // Default by node-imap
  authTimeout: 5000, // Default by node-imap,
  debug: console.log, // Or your custom function with only one incoming argument. Default: null
  autotls: 'never', // default by node-imap
  tlsOptions: { rejectUnauthorized: false },
  mailbox: "INBOX", // mailbox to monitor
  searchFilter: ["ALL"], // the search filter being used after an IDLE notification has been retrieved
  markSeen: true, // all fetched email willbe marked as seen and not fetched next time
  fetchUnreadOnStart: true, // use it only if you want to get all unread email on lib start. Default is `false`,
  attachments: true, // download attachments as they are encountered to the project directory
  attachmentOptions: { directory: "attachments/" } // specify a download directory for attachments
});

mailListener.start(); // start listening

// stop listening
//mailListener.stop();

mailListener.on("server:connected", function(){
  console.log("imapConnected");
});

mailListener.on("mailbox", function(mailbox){
  console.log("Total number of mails: ", mailbox.messages.total); // this field in mailbox gives the total number of emails
});

mailListener.on("server:disconnected", function(){
  console.log("imapDisconnected");
});

mailListener.on("error", function(err){
  console.log(err);
});

mailListener.on("headers", function(headers, seqno){
  // do something with mail headers
});

mailListener.on("body", function(body, seqno){
  // do something with mail body
})

mailListener.on("attachment", function(attachment, path, seqno){
  // do something with attachment
});

mailListener.on("mail", function(mail, seqno) {
  // do something with the whole email as a single object
})

// it's possible to access imap object from node-imap library for performing additional actions. E.x.
mailListener.imap.move(:msguids, :mailboxes, function(){})

```

That's easy!

## Testing

A test script is available at test.js. Before using the test script, it is necessary to set the following environment variables:

-   IMAPUSER - IMAP account username.
-   IMAPPASS - IMAP account password.
-   IMAPHOST - IMAP server hostname (e.g. imap.example.com).

The test script assumes that the IMAP host supports TLS and that the port is the usual 993. These values can be changed in test.js if necessary.

To run the test script, simply execute:

```bash
export IMAPUSER='user@example.com' IMAPPASS='password' IMAPHOST='imap.example.com'; node test.js
```

## License

MIT
