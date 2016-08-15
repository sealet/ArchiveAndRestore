# Archive and Restore

These workflows are used to backup or archive (removing the current mezzanine file) the mezzanine file of an asset in Reach Engine into an archive location at /archive. They also include workflows for restoring an archived file to Reach Engine for futher use

## Import Order
### Archive Workflows
1. assetContentBackup.xml
2. resetArchiveStatus.xml
3. singleAssetArchive.xml
4. collectionArchive.xml
5. archiveTimeline.xml

### Restore Workflows
1. restoreAsset.xml
2. restoreCollection.xml
3. restoreTimeline.xml

## Requirements

### Archive
1. User selects the asset or collection to be worked on
2. User must input either Archive or Backup as an option.
3. User must input whether they want a backup to be overwritten if Backup is selected

### Restore
1. User selects the file or collection that will be restored.

## Testing
Import the workflows. 
Attempt to backup a file that is currently in an untouched state, already backed up state, and archived state.
Attempt to backup with overwrite a file that is currently in an untouched state, already backed up state, and archived state.
Attempt to archive a file that is currently in an untouched state, already backed up state, and archived state.
Attempt to restore a file that is currently in an untouched state, already backed up state, and archived state.

Repeat the above with Timelines, Images, Audio, Documents, Others, and Collections.

## Descriptions
### assetContentBackup.xml
Input: AssetContent

This workflow checks first to see if an asset should get a full backup or just needs to be truncated. If an asset is flagged for full backup it copies the file to the archive location and sets the restoreKey. If the archive process doesn't complete fully it will attempt two additional times to perform the archive. If the archive option is selected, it will also set the truncatedFlag and remove the mezznanine file.

### resetArchiveStatus.xml
Input: AssetContent

This workflow checks if a file currently exists in its mezzanine repository. If it does, it deletes any file found at the archive location for this asset and clears the restoreKey. If it does not it fails the workflow. If there is no file at the archive location then it just clears the restoreKey and ends.

### singleAssetArchive.xml
Input: AssetMaster, archive or backup, overwrite flag

This workflow outputs the current archive status of the asset selected. If the file is to be backed up and overwritten it calls the workflow "resetArchiveStatus" first and then "assetContentBackup". If the asset is not backed up, or backed up and flagged for archive the "assetContentBackup" workflow gets called. If nothing needs to be performed based on the input the workflow ends with nothing happening.

### collectionArchive.xml
Input: AssetCollection, archive or backup, overwrite flag

This workflow finds all the archive-able assets (everything except Clips and Projects) and sends them to the "singleAssetArchive" workflow.

### archiveTimeline.xml
Input: Timeline, archive or backup, overwrite flag

This workflow passes an asset to the "singleAssetArchive" workflow.

### restoreAsset.xml
Input: AssetMaster

This workflow tests if an asset is truncated. If it is, then it copies the archived file back to the mezzanine repository and sets the truncatedFlag to false.

### restoreCollection.xml
Input: AssetCollection

This workflow takes each archive-able asset in a collection and passes it to the "restoreAsset" workflow.

### restoreTimeline.xml
Input: Timeline

This workflow takes a timeline and passes it to the "restoreAsset" workflow.
