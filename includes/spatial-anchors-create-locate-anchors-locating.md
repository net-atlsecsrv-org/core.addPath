## Locate a cloud spatial anchor

Being able to locate a previously saved cloud spatial anchor is one of the main reasons for using Azure Spatial Anchors. There are several different ways you can locate a cloud spatial anchor. You can use one strategy on a watcher at a time.
- Locate anchors by identifier.
- Locate anchors connected to a previously located anchor. You can learn about anchor relationships [here](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Locate anchor using [Coarse relocalization](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Each time you locate an anchor, Azure Spatial Anchors will attempt to use the environment data collected to augment the visual information on the anchor. If you are having trouble locating an anchor, it can be useful to create an anchor, then locate it several times from different angles and lighting conditions.

If you are locating cloud spatial anchors by identifier, you will want to store the cloud spatial anchor identifier in your application's back-end service, and make it accessible to all devices that can properly authenticate to it. For an example of this, see [Tutorial: Share Spatial Anchors across devices](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instantiate an `AnchorLocateCriteria` object, set the identifiers you're looking for, and invoke the `CreateWatcher` method on the session by providing your `AnchorLocateCriteria`.