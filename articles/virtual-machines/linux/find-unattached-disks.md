---
title: Hitta och ta bort icke anslutna Azure-hanterade diskar och ohanterade diskar
description: Hitta och ta bort frånkopplade Azure-hanterade och ohanterade (VHD/Page blobbar) diskar med hjälp av Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 87fe277bbd2fa618d43ce3274c1d2c05a5d7b396
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660163"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Hitta och ta bort frånkopplade Azure-hanterade och ohanterade diskar med hjälp av Azure CLI
När du tar bort en virtuell dator (VM) i Azure tas som standard alla diskar som är kopplade till den virtuella datorn bort. Den här funktionen hjälper till att förhindra data förlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator har tagits bort fortsätter du att betala för ej anslutna diskar. Den här artikeln visar hur du hittar och tar bort alla frånkopplade diskar och minskar onödiga kostnader. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: hitta och ta bort ej anslutna diskar 

Följande skript söker efter ej anslutna [hanterade diskar](managed-disks-overview.md) genom att undersöka värdet för egenskapen **ManagedBy** . När en hanterad disk är ansluten till en virtuell dator innehåller egenskapen **ManagedBy** resurs-ID för den virtuella datorn. När en hanterad disk är frånkopplad är egenskapen **ManagedBy** null. Skriptet undersöker alla hanterade diskar i en Azure-prenumeration. När skriptet hittar en hanterad disk med egenskapen **ManagedBy** inställt på null, fastställer skriptet att disken är frånkopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange **deleteUnattachedDisks** -variabeln till 0. Med den här åtgärden kan du söka efter och Visa alla ej anslutna hanterade diskar.
>
>När du har granskat alla de ej anslutna diskarna kör du skriptet igen och anger variabeln **deleteUnattachedDisks** till 1. Med den här åtgärden kan du ta bort alla frånkopplade hanterade diskar.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: hitta och ta bort ej anslutna diskar 

Ohanterade diskar är VHD-filer som lagras som [Page blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure Storage-konton](../../storage/common/storage-account-overview.md). Följande skript söker efter ej anslutna ohanterade diskar (Page blobbar) genom att undersöka värdet för egenskapen **LeaseStatus** . När en ohanterad disk är ansluten till en virtuell dator är egenskapen **LeaseStatus** inställd på **låst**. När en ohanterad disk är frånkopplad är egenskapen **LeaseStatus** inställd på **olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure Storage-konton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med egenskapen **LeaseStatus** inställd på **olåst**, fastställer skriptet att disken är frånkopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange **deleteUnattachedVHDs** -variabeln till 0. Med den här åtgärden kan du söka efter och Visa alla frånkopplade ohanterade virtuella hård diskar.
>
>När du har granskat alla de ej anslutna diskarna kör du skriptet igen och anger variabeln **deleteUnattachedVHDs** till 1. Med den här åtgärden kan du ta bort alla frånkopplade ohanterade virtuella hård diskar.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [ta bort ett lagrings konto](../../storage/common/storage-account-create.md#delete-a-storage-account).


