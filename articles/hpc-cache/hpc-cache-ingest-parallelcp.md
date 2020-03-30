---
title: Azure HPC-cachedata intas - parallellkopieringsskript
description: Så här använder du ett parallellkopieringsskript för att flytta data till ett Blob-lagringsmål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90e05ad3d42b1009b631630fe476669a9f418d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74166892"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC-cachedata intas - parallell kopiering skript metod

Den här artikeln innehåller ``parallelcp`` instruktioner för hur du skapar skriptet och använder det för att flytta data till en Blob-lagringsbehållare för användning med Azure HPC-cache.

Mer information om hur du flyttar data till Blob-lagring för Din Azure HPC-cache läser du [Flytta data till Azure Blob storage](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Skapa parallellcp-skriptet

Skriptet nedan kommer att `parallelcp`lägga till den körbara . (Det här skriptet är utformat för Ubuntu, ``parallel`` om du använder en annan distribution måste du installera separat.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Exempel på parallellkopia

I det här exemplet används ``glibc`` parallellkopieringsskriptet för kompilering med hjälp av källfiler i Azure HPC-cachen.

Källfilerna cachelagras i Azure HPC Cache-monteringspunkten och objektfilerna lagras på den lokala hårddisken.

I det här exemplet används ``-j`` parallellkopieringsskriptet med alternativet och ``make`` för att få parallellisering.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
