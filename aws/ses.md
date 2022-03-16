# Amazon Simple Email Service

Amazon Simple Email Service (SES) is a cost-effective, flexible, and scalable email service that enables developers to send mail from within any application. You can configure Amazon SES quickly to support several email use cases, including transactional, marketing, or mass email communications. Amazon SES's flexible IP deployment and email authentication options help drive higher deliverability and protect sender reputation, while sending analytics measure the impact of each email. With Amazon SES, you can send email securely, globally, and at scale.

### Link [here](https://aws.amazon.com/ses/)

We use the following approach to send emails with aws ses
We also use `mjml` with is a email template engine that help us to build emails templates lisnk [here](https://mjml.io/)

```ts
import {
  SendEmailCommandOutput,
  SESClient,
  SendEmailCommand,
  SendEmailCommandInput,
} from "@aws-sdk/client-ses";
import {
  AWS_SES_DEFAULT_EMAIL_SENDER,
  EMAIL_AWS_ACCESS_KEY_ID,
  EMAIL_AWS_SECRET_ACCESS_KEY,
} from "../constants";
import mjml2html from "mjml";

// Set the AWS Region.
const REGION = "us-east-1"; //e.g. "us-east-1"
// Create SES service object.
const sesClient = new SESClient({
  region: REGION,
  credentials: {
    accessKeyId: EMAIL_AWS_ACCESS_KEY_ID,
    secretAccessKey: EMAIL_AWS_SECRET_ACCESS_KEY,
  },
});
export { sesClient };

/**
 * @param {object} email - Email data.
 * @param {string} email.mjml - Email mjml input.
 * @param {string} email.subject - Subject.
 * @param {string} email.recipient - Recipient.
 * @returns {void} - Nothing.
 */
export async function sendEmail(email: {
  mjml: string;
  subject: string;
  recipient: string;
}): Promise<SendEmailCommandOutput> {
  const params: SendEmailCommandInput = {
    Message: {
      Body: {
        Html: {
          Charset: "UTF-8",
          Data: mjml2html(email.mjml).html,
        },
      },
      Subject: {
        Charset: "UTF-8",
        Data: email.subject,
      },
    },
    Destination: {
      ToAddresses: [email.recipient],
    },
    // Source:  email sender
    Source: AWS_SES_DEFAULT_EMAIL_SENDER,
  };

  try {
    const data = await sesClient.send(new SendEmailCommand(params));
    console.log("Success.2", data);
    return data;
  } catch (err) {
    console.log("Error", err);
  }
  throw Error("email not sent");
}
```

Then the usage es very simple
Mjml alse has a [desktop app](https://mjml.io/download) that can help with the template creation.
You can check the [documentation](https://documentation.mjml.io/) to learn how to build the templates
With the template string we pass it to the function to send the email
<img width="1678" alt="image" src="https://user-images.githubusercontent.com/34176666/158641296-33c1a450-f515-46f6-906a-56e92a6124ac.png">

```ts
// email-webhook.ts
try {
  await sendEmail({
    recipient: data.email as string,
    subject: "Email Subject",
    mjml: `<mjml>
        <mj-head>
            <mj-attributes>
            <mj-text align='center' color='#555' />
            </mj-attributes>
        </mj-head>
        <mj-body background-color='#eee'>
            <mj-section background-color='#3A526A'>
            <mj-column>
            <mj-image width='200px' src='https://image.path'></mj-image>
            </mj-column>
            </mj-section>
            <mj-section background-color='#fff'>
            <mj-column>
                <mj-text align='center'>
                <h2>Email Title</h2>
                </mj-text>
                <mj-text align='center'>
                <p>Some text</p>
                <p>lorem</p>
                <a href='${dynamicVarValue}' target='_blank'>Link Text</a>
                </mj-text>
            </mj-column>
            </mj-section>
        </mj-body>
        </mjml>`,
  });
} catch (e) {
  // log send email error
}
```


https://user-images.githubusercontent.com/34176666/158645394-bbf09426-fc07-4b36-ab2a-f4da72b54d7e.mp4



