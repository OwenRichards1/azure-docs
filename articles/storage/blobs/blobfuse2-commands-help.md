---
title: How to use BlobFuse2 help to get help info for the BlobFuse2 command and subcommands | Microsoft Docs
titleSuffix: Azure Blob Storage
description: Learn how to use BlobFuse2 help to get help info for the BlobFuse2 command and subcommands.
author: jimmart-dev
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 08/01/2022
ms.author: jammart
ms.reviewer: tamram
---

# BlobFuse2 help command

Use the `blobfuse2 help` command to get help info for the BlobFuse2 command and subcommands.

> [!IMPORTANT]
> BlobFuse2 is the next generation of BlobFuse and is currently in preview.
> This preview version is provided without a service level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> BlobFuse v1 is generally available (GA). For information about the GA version, see:
>
> - [The BlobFuse v1 project on GitHub](https://github.com/Azure/azure-storage-fuse/tree/master)
> - [The BlobFuse v1 setup documentation](storage-how-to-mount-container-linux.md)

## Syntax

The `blobfuse2 help` command has 2 formats:

`blobfuse2 help --[flag-name]=[flag-value]`

`blobfuse2 help [command] --[flag-name]=[flag-value]`

## Arguments

`[command]`

You can get help information for any of the specific BlobFuse2 commands. The supported `blobfuse2` commands are:

| Command | Description |
|--|--|
| [mount](blobfuse2-commands-mount.md)             | Mounts blob storage containers and displays existing mount points                         |
| [unmount](blobfuse2-commands-unmount.md)         | Unmounts previously mounted blob storage containers                                       |
| [mountv1](blobfuse2-commands-mountv1.md)         | Generates a configuration file for BlobFuse2 from a BlobFuse v1 configuration file        |
| [completion](blobfuse2-commands-completion.md)   | Generates the autocompletion script for BlobFuse2 for a specified shell                   |
| [secure](blobfuse2-commands-secure.md)           | Encrypts, decrypts, or accesses settings in a BlobFuse2 configuration file                |
| [version](blobfuse2-commands-version.md)         | Displays the current version of BlobFuse2, and optionally checks for the latest version   |

Select one of the command links in the table above to view the documentation for the individual commands, including the arguments and flags they support.

## Flags (options)

The flags available for `blobfuse2 help` are inherited from the parent command, [`blobfuse2`](blobfuse2-commands.md).

### Flags inherited from the BlobFuse2 command

The following flags are inherited from parent command [`blobfuse2`](blobfuse2-commands.md)):

| Flag | Short version | Value type | Default value | Description |
|--|--|--|--|--|
| disable-version-check |    | boolean | false | Enables or disables automatic version checking of the BlobFuse2 binaries |
| help                  | -h | n/a     | n/a   | Help info for the blobfuse2 command and subcommands                      |

## Examples

Get general BlobFuse2 help:

`blobfuse2 help`

Get help for the `blobfuse2 mount` command:

`blobfuse2 help mount`

Get help for the `blobfuse2 secure encrypt` subcommand:

`blobfuse2 help secure encrypt`

## See also

- [What is Blobfuse2?](blobfuse2-what-is.md)
- [The Blobfuse2 command set](blobfuse2-commands.md)
