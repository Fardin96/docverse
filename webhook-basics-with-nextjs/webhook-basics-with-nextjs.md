# Webhook Basics with Next.js

## 1. Overview

This guide explains how to set up a webhook for a Next.js application and deploy it to Vercel. Webhooks allow your application to respond to events, such as pushes to a GitHub repository, triggering automatic deployments. For more information on webhooks, visit [Webhooks.fyi](https://webhooks.fyi/).

## 2. Setting Up Your Next.js Application

1. **Create a Next.js App:**
   If you haven't already, create a new Next.js application using the following command:
   ```bash
   npx create-next-app@latest my-next-app
   cd my-next-app
   ```

2. **Deploy to Vercel:**
   - Sign up or log in to [Vercel](https://vercel.com).
   - Import your Next.js project from GitHub.
   - Follow the prompts to deploy your application.

## 3. Configuring Webhooks in GitHub

Webhooks in GitHub can trigger deployments to Vercel when changes are pushed to your repository.

### Steps to Set Up Webhooks in GitHub:

1. **Locate Your Vercel Project:**
   Open your project in Vercel. The URL will be `https://vercel.com/<your-username>/<your-project>`.

2. **Configure the Webhook in GitHub:**
   - Go to your GitHub repository.
   - Navigate to `Settings > Webhooks > Add webhook`.
   - Payload URL: Use the Vercel webhook URL found in your project settings.
   - Content type: Select `application/json`.
   - Events: Choose `Just the push event`.
   - Save the webhook.

3. **Verify Webhook Setup:**
   In Vercel, check the project settings to see if the webhook events are being received.

## 4. Testing the Webhook

After setting up the webhook, test it by pushing changes to your repository.

### Steps to Test:

1. **Make a Change:**
   Edit a file in your Next.js app, then commit and push the changes:
   ```bash
   git add .
   git commit -m "Test webhook deployment"
   git push origin main
   ```

2. **Check Vercel Dashboard:**
   Open your Vercel project dashboard to see if a new deployment was triggered.

## 5. Common Issues and Troubleshooting

1. **Webhook Not Triggering:**
   - Ensure the webhook URL is correct and that the events are properly configured in GitHub.

2. **Deployment Fails:**
   - Check the Vercel logs for any errors during the deployment process.

3. **Permissions Issues:**
   - Ensure that your GitHub repository has the necessary permissions to trigger the webhook.

For more detailed instructions, refer to the [Vercel documentation](https://vercel.com/docs).
