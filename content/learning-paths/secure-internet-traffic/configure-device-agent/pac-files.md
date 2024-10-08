---
title: Determine when to use PAC files
pcx_content_type: learning-unit
weight: 6
layout: learning-unit
---

{{<Aside type="note">}}

Only available on Enterprise plans.

{{</Aside>}}

Learn how and when to use PAC files instead of (or complementary to) endpoint agents.

## What are PAC files?

A {{<glossary-tooltip term_id="PAC file">}}PAC file{{</glossary-tooltip>}}, or proxy auto-configuration file, is like a tiny map that guides your web browser to websites. Instead of going straight to a website, a PAC file can forward your traffic through a proxy server first, protecting your device and filtering unwanted URL access. Cloudflare users use PAC files to filter Internet traffic when they do not want to install agents on devices or where agent installations are not supported.

Here is a quick overview of PAC files:

- **What they do**: PAC files contain JavaScript code that decides whether or not your browser should use a proxy. The code determines this for each website you visit.
- **How they work**: PAC files tell your browser to run a `FindProxyForURL()` function with the website address. This function analyzes the address and decides whether to send it directly to the browser or through a specified proxy server.
- **Why use them**: PAC files are handy for organizations or networks that want to control access to the Internet. PAC files can allow access to some websites directly while routing others through the proxy for filtering or security.
- **Benefits**: Managing a single PAC file saves time and effort compared to manually configuring proxy settings for each device. It also allows for flexible rules based on websites, time and date, and other factors.

Think of PAC files like a GPS: you are driving to a friend's house, but there is construction on the main road. Your GPS (the PAC file) suggests a detour through a side street (the proxy server) to get there faster.

### Use cases

 Some use cases for PAC files include:

- **Versions of Windows before Windows 8/Windows Server 2012**: The WARP client does not support older versions of Windows, so PAC files provide a clientless solution to route traffic through Cloudflare to add security and filtering benefits.
- **Non-persistent virtual desktop infrastructure (VDI) environments**: PAC files can be especially valuable in non-persistent VDI environments where installing and saving user details for the WARP client is challenging. In these instances, PAC files ensure consistent access and security regardless of individual user sessions.
- **Backup in case of agent outage**: In case of an agent outage, PAC files can act as a backup that can be deployed quickly to minimize downtime and security risk.

## Where are PAC files hosted?

PAC files are usually hosted in a centralized location where all of the devices in your organization can reach and download the file. You can configure browsers with a PAC URL to retrieve the PAC file from the address. This typically occurs when your users open the browser. Many admins push PAC files to devices via deployment methods such as Group Policy Objects (GPOs).

## Create a PAC file

For detailed instructions on creating a PAC file, refer to [Enable Gateway proxy with PAC files](/cloudflare-one/connections/connect-devices/agentless/pac-files/).

### Best practices

- Avoid complex logic and nested conditions, as they might slow down processing time.
- Place frequently accessed URLs and conditions at the top for faster processing.
- Test your PAC file logic on multiple devices before deployment with tools such as an [online proxy PAC file tester](https://thorsen.pm/proxyforurl).
- When users download a PAC file from a central location, the download must complete within 30 seconds or most browsers will time out.
- Requests must complete with an HTTP response code `200`.
- Requests must have an uncompressed body smaller than 1 MB (megabyte).
- Do not include standard HTTP caching within your PAC file. Cached contents can make PAC instructions outdated, and thus lead to bad HTTP routing.
- PAC files cannot be fetched through a proxy.
