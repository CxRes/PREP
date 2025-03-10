# Design {#design}

The {{&protocol}} is predicated on a resource being the most intuitive source for notifications about its own updates.

This is unlike other notification protocols that require additional resources to be specifically dedicated as endpoints for delivering notifications. Servers that provide updates are forced to maintain these additional endpoints and clients that consume these updates have to co-ordinate data between the endpoint and a resource of interest.

By giving every resource the ability to send notifications when it updates, the {{&protocol}} aims to reduce the complexity of  both servers and clients implementing notifications; thus, making it easier for developers to build and maintain real-time applications.

## Principles {#principles}

The {{&protocol}} treats notifications as a temporally extended representation of any resource. That is, a representation can describe not (just) the state of the resource but events on the resource. With HTTP allowing representations to provide a potentially unbounded stream of data, the {{&protocol}} is able to communicate events on the resource as notifications.

## Goals {#goals}

The goals of the {{&protocol}} are:

+ to provide notifications only using the HTTP protocol [STD97](https://www.rfc-editor.org/info/std97) {{HTTP}} so that the clients are able to request for update notifications using the Fetch API {{FETCH}}.
+ to provide updates directly from the resource of interest, obliviating the need to connect to another endpoint for notifications, minimizing round-trips between clients and servers and the need to co-ordinate responses between a resource and the endpoint.
+ to allow arbitrary representations for notifications. Implementers shall be able to provide notifications that are potentially more expressive when compared to existing HTTP based messaging protocols such as Server Sent Events {{SSE}}.

## Constraints {#constraints}

To the extent feasible, the {{&protocol}}:

+ adheres to established practices and conventions. In particular, every attempt has been made to reuse existing protocols and specifications. Implementers shall be able to repurpose existing tools and libraries for implementing this specification.
+ conforms to the REST Architectural Style {{REST}} and best practices for Building Protocols with HTTP {{RFC9205}}. This specification can, thus, be used to extend the capabilities of any existing HTTP resource to provide notifications. Implementers shall be able to scale notifications along with their data/applications.
  <!--
    See my original comment on the Solid/Specification Gitter channel on 24 April 2020
    https://matrix.to/#/!PlIOdBsCTDRSCxsTGA:gitter.im/$VgCcuq2HbpLKJvxIw4witAUOsqcdhC98glgzqVI1WOY?via=gitter.im
  -->

## Known Limitations {#known-limitations}

The {{&protocol}} only allows notifications to be sent for events on a given resource. It is not possible for resources to send notifications for arbitrary events such as state changes on multiple resources or combinations thereof. Implementations using the {{&protocol}} have to create separate resources to realize such notification endpoints. But this is no different from APIs built on top of existing messaging protocols (See, for example, {{WS}} and {{WEBSUB}}).

Browsers cap the number of persistent HTTP/1.1 connections per host, limiting the suitability of the {{&protocol}} for web applications in the browser that simultaneously require notifications from multiple resources on the same host. This limitation is identical to that of other HTTP streaming based protocols, such as Server-Sent Events {{SSE}}. Implementations are strongly encouraged to adopt HTTP/2 (or later). HTTP/1.1 servers might consider setting up a reverse proxy to serve PREP notifications over HTTP/2 (or later) or implement mitigation strategies, such as to maximize the number of concurrent connections and to provide alternate hosts for resources. Browser clients receiving PREP notifications over an HTTP/1.1 connection are advised to exercise caution when opening multiple simultaneous persistent connections to any given host.