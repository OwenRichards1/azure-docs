---
title: How to use the BlobFuse2 command set | Microsoft Docs
titleSuffix: Azure Blob Storage
description: Learn how to use the BlobFuse2 command set to mount blob storage containers as file systems on Linux, and manage them.
author: jimmart-dev
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 08/01/2022
ms.author: jammart
ms.reviewer: tamram
---

# How to use the BlobFuse2 command set

This reference shows how to use the BlobFuse2 command set to mount Azure blob storage containers as file systems on Linux, and how to manage them.

> [!IMPORTANT]
> BlobFuse2 is the next generation of BlobFuse and is currently in preview.
> This preview version is provided without a service level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> If you need to use BlobFuse in a production environment, BlobFuse v1 is generally available (GA). For information about the GA version, see:
>
> - [The BlobFuse v1 setup documentation](storage-how-to-mount-container-linux.md)
> - [The BlobFuse v1 project on GitHub](https://github.com/Azure/azure-storage-fuse/tree/master)

## Syntax

The `blobfuse2` command has 2 formats:

`blobfuse2 --[flag-name]=[flag-value]`

`blobfuse2 [command] [arguments] --[flag-name]=[flag-value]`

## Flags (Options)

Most flags are specific to individual BlobFuse2 commands. See the documentation for [each command](#commands) for details and examples.

The following options can be used without a command or are inherited by individual commands:

| Flag | Short version | Value type | Default value | Description |
|--|--|--|--|--|
| disable-version-check |    | boolean | false | Whether to disable the automatic BlobFuse2 version check |
| help                  | -h | n/a     | n/a   | Help info for the blobfuse2 command and subcommands      |
| version               | -v | n/a     | n/a   | Display BlobFuse2 version information                    |

### Commands

The supported commands for BlobFuse2 are:

| Command | Description |
|--|--|
| [mount](blobfuse2-commands-mount.md)           | Mounts an Azure blob storage container as a filesystem in Linux or lists mounted file systems |
| [mountv1](blobfuse2-commands-mountv1.md)       | Mounts a blob container using legacy BlobFuse configuration and CLI parameters |
| [unmount](blobfuse2-commands-unmount.md)       | Unmounts a BlobFuse2-mounted file system |
| [completion](blobfuse2-commands-completion.md) | Generates an autocompletion script for BlobFuse2 for the specified shell |
| [secure](blobfuse2-commands-secure.md)         | Encrypts or decrypts a configuration file, or gets or sets values in an encrypted configuration file |
| [version](blobfuse2-commands-version.md)       | Displays the current version of BlobFuse2 |
| [help](blobfuse2-commands-help.md)             | Gives help information about any command |

## Arguments

BlobFuse2 command arguments are specific to the individual commands. See the documentation for [each command](#commands) for details and examples.

## See also

- [What is BlobFuse2?](blobfuse2-what-is.md)
- [How to mount an Azure blob storage container on Linux with BlobFuse2](blobfuse2-how-to-deploy.md)
- [BlobFuse2 configuration reference](blobfuse2-configuration.md)
- [How to troubleshoot BlobFuse2 issues](blobfuse2-troubleshooting.md)