---
title: Enable Microsoft Azure
pcx_content_type: how-to
weight: 63
meta:
  title: Enable Logpush to Microsoft Azure
---

# Enable Logpush to Microsoft Azure

Cloudflare Logpush supports pushing logs directly to Microsoft Azure via the Cloudflare dashboard or via API.

## Manage via the Cloudflare dashboard

Enable Logpush to Microsoft Azure via the dashboard.

To enable the Cloudflare Logpush service:

{{<render file="_enable-logpush-job.md">}}

7. In **Select a destination**, choose **Microsoft Azure**.

8. Enter or select the following destination information:

    - **SAS URL**
    - **Blob container subpath (optional)**
    - **Daily subfolders**

9. Select **Validate access**.

10. Enter the **Ownership token** (included in a file or log Cloudflare sends to your provider) and select **Prove ownership**. To find the ownership token, select **Open** in the **Overview** tab of the ownership challenge file.

11. Select **Save and Start Pushing** to finish enabling Logpush.

Once connected, Cloudflare lists Microsoft Azure as a connected service under **Logs** > **Logpush**. Edit or remove connected services from here.

## Create and get access to a Blob Storage container

Cloudflare uses a shared access signature (SAS) token to gain access to your Blob Storage container. You will need to provide `Write` permission and an expiration period of at least five years, which will allow you to not worry about the SAS token expiring.

{{<render file="_enable-read-permissions.md">}}
<br/>

To enable Logpush to Azure:

1. Create a Blob Storage container. Refer to [instructions from Azure](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal).

2. Create a [shared access signature (SAS)](https://learn.microsoft.com/en-us/azure/storage/common/storage-sas-overview) to secure and restrict access to your blob storage container. Use [Storage Explorer](https://learn.microsoft.com/en-us/azure/storage/storage-explorer/vs-azure-tools-storage-manage-with-storage-explorer) to navigate to your container and right click to create a signature. Set the signature to expire at least five years from now and only provide write permission.

3. Provide the SAS URL when prompted by the Logpush API or UI.

{{<Aside type="note" header="Note">}}

Logpush will stop pushing logs if your SAS token expires, which is why an expiration period of at least five years is required. The renewal for your SAS token needs to be done via API, updating the `destination_conf` parameter in your Logpush job.

{{</Aside>}}