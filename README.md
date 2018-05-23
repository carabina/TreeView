Swift notes
---

TreeView is written in Objective-C and fully compatibile with Swift.
Check out [swift-example](https://github.com/genkernel/TreeView/tree/swift-example) branch to see how well it plays with Swift.


TreeView
========

Component that introduces cells + subcells support for any UITableView living there in a controller's view.

Example on Youtube: http://youtu.be/zS3gQ4pnmBs

TreeView is a "proxy" object that sits between table view and a view controller, proxies all calls to data source and converts 2d-like indexPaths (0-0, 0-1, ...)  into N-depth indexPaths (0-0, 0-0-1, 0-0-2, 0-1-0-1, ...).


You usually use TreeView component when your <b>UITableViewCell</b> wants to contain its own subcells that can be easily shown / hidden.<br />


Examples
---
Take a look at 3 branches: [fsTree-example](https://github.com/genkernel/TreeView/tree/fsTree-example), [allExpanded-example](https://github.com/genkernel/TreeView/tree/allExpanded-example) and [plistDatasource-example](https://github.com/genkernel/TreeView/tree/plistDatasource-example) to get inside view on how to implement subcells support for a table view.


Implementation details
---

TreeView adds 2 logical states to every cell: <b>expanded</b> and <b>collapsed</b>.

You should expand cell to reveal its subcells.<br/>
Keeping this in mind helper methods were implemented: <br/>
```objectiveC
- (void)expand:(NSIndexPath *)treeIndexPath;
- (BOOL)isExpanded:(NSIndexPath *)treeIndexPath;
- (void)collapse:(NSIndexPath *)treeIndexPath;
```

Instead of implementing <b>UITableViewDataSource</b> in your controller - change it to <b>TreeTableDataSource</b>. TreeTableDataSource protocol extends UITableViewDataSource by introducing 2 new methods:<br/>
```objectiveC
@required
- (BOOL)tableView:(UITableView *)tableView isCellExpanded:(NSIndexPath *)treeIndexPath;
- (NSUInteger)tableView:(UITableView *)tableView numberOfSubCellsForCellAtIndexPath:(NSIndexPath *)treeIndexPath;
```

Notice all @required dataSource methods are invoked with indexPath of N-depth that uniquely identify cell or subcell.<br/>
Hence you should change behaviour of the following methods:
```objectiveC
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)treeIndexPath;
```

and use 
```objectiveC
- (NSIndexPath *)tableIndexPathFromTreePath:(NSIndexPath *)treeIndexPath
```
if you need to convert N-depth index path into 2d index path.

On the other hand all @optional methods are transparently forwarded to your implementations (if such exists) and indexPath parameter is not changed - it is 2d indexPath.
You can convert it into N-depth indexPath with:
```objectiveC
- (NSIndexPath *)treeIndexPathFromTablePath:(NSIndexPath *)treeIndexPath;
```
method.


## Installation

TreeView is available through [CocoaPods](http://cocoapods.org/pods/treeview). To install
it, simply add the following line to your Podfile:

```ruby
pod 'TreeView'
```

Conclusion
---

With TreeView you can have any cells-subcells levels number. For example:<br />
Cells levels and its indexPaths representation:
  - section 0
      - [0, 0]
      - [0, 1]
      - [0, ...]
  - section 1
      - [1, 0]
      - [1, 1]
          - [1, 1, 0]
          - [1, 1, 1]
          - [1, 1, ...]
      - [1, 2]
          - [1, 2, 0]
  - section 2
      - [2, 0]
      - [2, 1]
      - [2, 2]
      - [2, 3]
      - [2, ...]
  - [...]
<br />
<br />
With UITableView data srtucture exposed via 2d indexPaths only. For example:
- section 0
  - [0, 1]
  - [0, 2]
  - [0, 3]
  - [0, ...]
- section 1
  - [1, 0]
  - [1, 1]
  - [1, 2]
  - [1, ...]
- section ...

See demo app example that represents this concept in action.

![Concept](https://github.com/genkernel/TreeView/raw/master/DemoArt/demo.gif)

TODO
---

<i>
* Test cells moving between sections.
</i>
