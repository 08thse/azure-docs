---
title: Azure Video Analyzer terminology
description: This article provides an overview of Azure Video Analyzer terminology.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 03/10/2021

---
# Terminology

This article provides an overview of terminology related to [Azure Video Analyzer](overview.md).

## Video

When you create an Video Analyzer account, you have to associate an Azure storage account with it. If you use Video Analyzer to record the live video from a camera, that data is stored as blobs in a container in the storage account. Videos are resources in your Video Analyzer account that map to such blob containers. All content associated with such video resources are stored as blobs in the corresponding containers, while Video Analyzer holds the metadata (such as a name, description, creation time).

You can use Video Analyzer to create video resources and add data to existing videos. This enables the scenarios of continuous and event-based video recording and playback (with video capture on the edge device, recording to Video Analyzer, and playback via Video Analyzer streaming capabilities).

## gRPC

[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework. It uses session-based structured schemas via [Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) as its underlying message interchange format for communication.

## Pipeline topology

[Pipeline topology](pipeline.md) lets you define where media should be captured from, how it should be processed, and where the results should be delivered. It enables you to define a graph consisting of sources, processors, and sink nodes and hence provides the ability for you to build live video analytics applications. 

## Recording

In the context of a video management system for security cameras, video recording refers to the process of capturing video from the cameras and storing it in a file (or files) for subsequent viewing via mobile and browser apps. Video recording can be categorized into [continuous video recording](continuous-video-recording.md) and [event-based video recording](event-based-video-recording-concept.md). These are explained in more detail in the [video recording](video-recording.md) concept page.

## RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) refers to Real-Time Streaming Protocol. It is an application-level protocol for control over the delivery of data with real-time properties. RTSP provides an extensible framework to enable controlled, on-demand delivery of real-time data, such as audio and video. 

## Streaming

You can use Video Analyzer to stream video to clients using industry-standard, HTTP-based media streaming protocols like [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) and [MPEG-DASH](https://dashif.org/about/). HLS is supported by web-players like J[JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Google’s Shaka Player](https://github.com/google/shaka-player), or you can render natively in mobile apps with Android's [Exoplayer](https://github.com/google/ExoPlayer) and iOS's [AV Foundation](https://developer.apple.com/av-foundation/). MPEG-DASH is likewise supported by [a list of clients on this page](https://dashif.org/clients/). You can learn more about that in the [video playback](video-playback.m) article.

## VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) refers to a Video Management System. Such systems are used to configure and control CCTV cameras, capture and record videos from them. These systems also provide client applications to play back the recorded video

## Next steps

[Pipeline topology](pipeline.md)
