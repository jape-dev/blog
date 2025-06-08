---
title: Making Authentication Simpler in MCP
date: 2025-06-08
---


If you're working with AI tools or data platforms, you might start hearing more about something called MCP – the Model Context Protocol. It's an open standard developed by Anthropic that aims to standardise how AI models connect with external tools, data sources, and systems.

But one topic that's been tripping people up is authentication—basically, how we make sure only the right people and apps get access.

Let's talk about how to make this simpler, not harder.

## Some definitions

### MCP Clients
MCP Clients are AI applications or systems that consume external resources through the MCP protocol. They initiate requests to MCP servers to access data or execute functions.

Examples of MCP clients:
* Claude Desktop app (can connect to MCP servers to access local files, databases, or APIs)
* AI coding assistants that need to access project repositories or development tools

### MCP Servers
MCP Servers are services that expose resources, data, or functionality to MCP clients through the standardised protocol. They act as intermediaries between the AI client and the actual data sources or tools.

Examples of MCP servers:
* A database MCP server that provides secure access to SQL databases
* A Git MCP server that provides access to version control operations
* A calendar MCP server that integrates with Google Calendar or Outlook

MCP works through a client-server architecture where communication happens via a standardised protocol, allowing AI applications to retrieve context, execute functions, and access real-time data from external systems.

## The USB Analogy

Think of MCP like USB ports and cables.

Before USB, every device had its own unique connector - printers had parallel ports, keyboards had PS/2 ports, mice had serial ports, etc. If you wanted to connect a new device to your computer, you needed the right specific port and cable for that exact device.

USB standardised this by creating:
* One universal connector type that works with any USB device
* A common protocol for how devices communicate with computers

Now with MCP:
* MCP Clients (AI applications) are like computers with USB ports - they have a standardized way to connect to external resources
* MCP Servers are like USB adapters/hubs - they take various external services (databases, APIs, file systems) and make them available through the standard MCP "connector"

![MCP Architecture Diagram](https://composio.dev/wp-content/uploads/2025/03/Noah-MCP.png)
*Image Credit: Norah Sakal*

## Why authentication is a concern

There are a few noted security concerns when it comes to MCP, but the one I want to focus on is unauthorised data access. As MCP-enabled tools interact with sensitive data, they increase the risk of unauthorised access, especially when AI systems gain broad permissions to corporate resources.

Therefore, many people are worried that every MCP server also needs to be in charge of verifying user identities and provisioning permissions/scopes. That sounds like a lot of work and trust needed to be provided to the developer of the MCP server. But here's the good news: it doesn't have to be that complicated.

MCP can rely on something we've been using for a long time: OAuth, a popular standard for handling secure logins and permissions.

## OAuth is like a hotel keycard system

When you check into a hotel (app), you're given a keycard (access token) that only opens your room (specific resources), for a limited time.

You don't get access to the whole hotel — just the room you paid for. And the card can be revoked or expire at any time.

If you ask the front desk (authorization server) to give your friend (another app) access to your room, they'll issue them their own keycard with only the permissions you approved — maybe just to drop off a bag, not to stay overnight.

![OAuth Flow Diagram](https://a.slack-edge.com/fbd3c/img/api/articles/oauth_scopes_tutorial/slack_oauth_flow_diagram.png)
*Image Credit: [Slack](https://api.slack.com/legacy/oauth)*

## Why Is This Helpful?

Right now, the MCP spec kind of assumes your MCP server is doing everything: issuing tickets (access tokens) and enforcing them. That's what creates confusion. But these are actually two distinct roles in OAuth 2.0: the authorization server issues tokens, and the resource server (the API or MCP server) checks them. In older systems, those two roles were often handled by the same service—but that model doesn't scale well in today's dynamic, distributed environments.

### Protected Resource Metadata (PRM)

This is where Protected Resource Metadata (PRM) comes in. PRM (defined in RFC 9728) lets an API—like an MCP server—publish a discovery document at a well-known URL that says:

> "Hey, here's how I expect clients to authenticate. These are the scopes I require. Here are the authorisation servers I trust. And here's how tokens should be formatted and presented."

With PRM, the MCP server no longer needs to hardcode or guess what auth infrastructure the client should use. Instead, it can delegate that responsibility. 

For example, an MCP server hosting a financial data API could simply point to `https://auth.bank.com` as its trusted authorisation server, say it requires `finance.read` scopes, and specify that DPoP-bound tokens must be used. A client can discover all this at runtime by fetching the `.well-known/oauth-protected-resource` document.

This makes the API truly self-describing, which is essential for agentic clients, AI integrations, and no-code tools that can't rely on preconfigured settings. With this metadata in place, OAuth clients can automatically figure out where to get a token, how to request the right scopes, and how to present that token—without manual setup or brittle assumptions.

In the context of MCP, PRM ensures that clients know exactly how to access a protected resource, even if the ticket issuer (authorisation server) is hosted elsewhere. This clean separation of responsibilities supports secure, flexible, and scalable integration patterns—exactly what modern apps and agents need.

It lets the MCP server say, "Hey, I'm just the hotel room. I'm not the front desk — but here's where you can go to get your keycard."

## What About Enterprise Logins?

In many companies, there's a standard way users and apps get access to services. This is often called Enterprise Single Sign-On (SSO). Here's how it works:

* The company uses a central login system — like Okta, Azure AD, or Google Workspace — to handle all user sign-ins. This is the Identity Provider (IdP).
* That same system also issues access tokens, using standards like OAuth 2.0 or SAML, to let users and apps talk to internal APIs.
* All the company's internal systems are manually configured to trust tokens from this IdP.

In this setup, everything is tightly controlled:
* The login system and APIs are set up ahead of time.
* IT teams make sure all services are properly connected.
* Apps don't need to "discover" anything — they already know where to get tokens and what the rules are.

In the context of MCP, this means enabling users to authenticate with MCP Servers using their enterprise IdP. If the application behind the MCP Server already supports enterprise SSO, no extra integration work is needed. When a user is redirected for authorisation, the MCP Authorisation Server can delegate login to the enterprise IdP.

## In conclusion

As MCP adoption grows, it's crucial to make authentication feel less like a barrier and more like a built-in feature. By leveraging existing standards like OAuth 2.0 and Protected Resource Metadata, developers can avoid reinventing the wheel and instead build secure, scalable, and user-friendly integrations. This separation of concerns—letting MCP servers focus on exposing resources while leaving identity and access management to trusted authorisation infrastructure—paves the way for more flexible and interoperable AI systems. Whether you're building for individual users or large enterprises with complex SSO setups, the path forward is clear: authentication can—and should—be simple.

