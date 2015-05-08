# angular-tree-table
[Angular 1.x] Display tree table &amp; event DrapnDrop, field 'td' by tree (other normal) - without jQuery.
## Current Version
- 2.0.1

## Demo: 
- http://thienhung1989.github.io/angular-tree-table/demo
- http://plnkr.co/edit/6zQNvW?p=preview

This product is the combination and optimization of 2: https://github.com/angular-ui-tree/angular-ui-tree (Drag2Drop) và https://github.com/khan4019/tree-grid-directive (Display Tree2Table).

## Install bower:
```js
bower angular-tree-table install
```

* Function 'filter' & 'group by' not add in ng-repeate (it's slow & incompatible with $id($$hash) )
* Able add function to tree-table by:
	* Example: *(See on Demo 2)*
```js
	$scope.my_tree.addFunction = function(b){
	  console.log(b);
	  alert('Function added in Controller "App.js"');
	}
```
		* Call function: *(tree.remove_branch extended see below with function other)*
```js
              $scope.col_defs = [
                    {
                        field: "Description"
                    }, {
                        displayName:  'Function',
                        cellTemplate: '<button ng-click="tree.addFunction(row)" class="btn btn-default btn-sm">Added Controller!</button>'
                    }, {
                        displayName:  'Remove',
                        cellTemplate: '<button ng-click="tree.remove_branch(row)" class="btn btn-default btn-sm">Remove</button>'
                    }];
```

* Functions extended in callback (able change by attribute):
```js
$scope.$callbacks = {
	// function accept called when item Drapping move-over target
	accept:      function (scopeDrag, scopeTarget, align) {
		return true;
	},
	
	beforeDrag:  function (scopeDrag) {
		return true;
	},
	dropped:     function (info, accepted) {
		return false;
	},
	dragStart:   function (event) {},
	dragMove:    function (event) {},
	dragStop:    function (event, skiped) {},
	beforeDrop:  function (event) {
		return true;
	},
	calsIndent:  function (level) {
		if (level - 1 < 1) {
			return $scope.indent_plus + ($scope.indent_unit ? $scope.indent_unit : 'px');
		} else {
			return ($scope.indent * (level - 1)) + $scope.indent_plus + ($scope.indent_unit ? $scope.indent_unit : 'px');
		}
	},
	dragEnabled: function () {
		return $scope.dragEnabled;
	}
};
```
* Functions extended in control (attribute 'tree-control'):
```html
	tree.expand_all();
	tree.collapse_all();
	tree.select_first_branch();
	tree.select_branch(branch);
	tree.add_branch(parent, new_branch, index);
	tree.add_root_branch(new_branch);
	tree.remove_branch(branch);
	tree.expand_branch(branch);
	tree.collapse_branch(branch);
	tree.get_selected_branch();
	tree.get_first_child(branch);
	tree.get_children(branch);
	tree.get_first_branch();
	tree.get_next_branch(branch);
	tree.get_prev_branch(branch);
	tree.get_parent_branch(branch);
	tree.get_siblings(branch);
	tree.get_next_sibling(branch);
	tree.get_prev_sibling(branch);
	tree.get_closest_ancestor_next_sibling(branch);
	tree.select_next_branch(branch);
	tree.select_next_sibling(branch);
	tree.select_prev_sibling(branch);
	tree.select_parent_branch(branch);
	tree.last_descendant(branch);
	tree.select_prev_branch(branch);
```

* Example
**init:
```html
 <tree-table
        class="table"
        tree-data="tree_data"
        tree-control="my_tree"
        callbacks="callbacks"
        drag-enabled="true"
        icon-leaf="none"
        icon-expand="fa fa-fw fa-angle-right"
        icon-collapse="fa fa-fw fa-angle-down"
        template-url="tree-table-template.html"
        column-defs="col_defs"
        expand-on="expanding_property"
        on-select="my_tree_handler(branch)"
        on-click="my_tree_handler(branch)"
        data-indent="30"
        data-indent-unit="px"
        data-indent-plus="15"
   >
</tree-table>
```
** Template Extened:
```html
<script type="text/ng-template" id="tree-table-template.html">
	<div class="tree-table">
            <table class="table">
                <thead>
                    <tr>
                        <th ng-class="expandingProperty.titleClass" ng-style="expandingProperty.titleStyle">
                            {{expandingProperty.displayName || expandingProperty.field || expandingProperty}}
                        </th>
                        <th ng-repeat="col in colDefinitions" ng-class="col.titleClass"
                            ng-style="col.titleStyle">
                            {{col.displayName || col.field}}
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <tr tree-table-node ng-repeat="row in tree_rows track by hashedTree(row)"
                        ng-show="row.__visible__" ng-class="(row.__selected__ ? ' active':'')"
                        class="ng-animate ">
                        <td ng-if="!expandingProperty.template"
                            ng-style="expandingProperty.cellStyle ? expandingProperty.cellStyle : {'padding-left': $callbacks.calsIndent(row.__level__)}"
                            ng-click="user_clicks_branch(row)" ng-class="expandingProperty.cellClass"
                            compile="expandingProperty.cellTemplate">
                        <span tree-table-node-handle>
                            <i class="fa fa-sort text-muted m-r-sm"></i>
                        </span> {{row[expandingProperty.field] || row[expandingProperty]}} <a> <i
                                ng-class="row.__tree_icon__" ng-click="row.__expanded__ = !row.__expanded__"
                                class="tree-icon"></i> </a>
                        </td>
                        <td ng-if="expandingProperty.template" compile="expandingProperty.template"></td>
                        <td ng-repeat="col in colDefinitions" ng-class="col.cellClass" ng-style="col.cellStyle"
                            compile="col.cellTemplate">
                            {{row[col.field]}}
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
	<pre>{{ treeData | json }}</pre>
	<pre>{{ tree_rows | json }}</pre>
</script>
```
## dragStop:
```js
    scope.$callbacks.dragStop(dragInfo, _status);
```
    * _status: Status changed pos of node, Drag succeed!
    * dragInfo:
        * node:          scope.node(), // Data node dragged
        * scope:         scope, // Scope node
        * level:         scope.node().__level__, // Level indent
        * target:        scope.prev(), // Node prev
        * move:
            * parent: -1, // -1: Dragged failed, null: node root, > 0: node normal
            * pos:    -1 // Position new Note moveTo


* Add 'data' to TreeTableNode  `tree-table-node=data` in template;
```html
<tr tree-table-node="row" ng-repeat="row in tree_rows track by hashedTree(row)"
                            ng-show="row.__visible__" ng-class="(row.__selected__ ? ' active':'')" class="ng-animate ">
    ....
</tr>
```

# Display ListTree (UL, OL)

* Combinding with list-tree.

```html
<script type="text/ng-template" id="tree-table-template-render.html">
    <ul tree-table-nodes="tree_data" class="tree-table-rows">
        <li tree-table-node="row" ng-repeat="row in datas track by row.__hashKey__" ng-show="row.__visible__"
            ng-class="(row.__selected__ ? ' active':'')"
            ng-style="expandingProperty.cellStyle ? expandingProperty.cellStyle : {}" ng-click="user_clicks_branch(row)"
            ng-class="expandingProperty.cellClass" compile="expandingProperty.cellTemplate"
            ng-include="'tree-table-template-fetch.html'"></li>
    </ul>
</script>
<script type="text/ng-template" id="tree-table-template-fetch.html">
    <a tree-table-node-handle class="btn btn-default"> - </a>{{row[expandingProperty.field] || row[expandingProperty]}}
    <a ng-if="row.__expanded__ != null" class="btn btn-default"
       ng-click="expand(row)"> {{ (row.__expanded__) ? '-' : '+' }} </a>
    <ul tree-table-nodes="row.__children__" class="tree-table-rows">
        <li tree-table-node="row" ng-repeat="row in datas track by row.__hashKey__" ng-show="row.__visible__"
            ng-class="(row.__selected__ ? ' active':'')"
            ng-style="expandingProperty.cellStyle ? expandingProperty.cellStyle : {}" ng-click="user_clicks_branch(row)"
            ng-class="[expandingProperty.cellClass]" compile="expandingProperty.cellTemplate"
            ng-include="'tree-table-template-fetch.html'"></li>
    </ul>
</script>
<tree-table tree-data="tree_data" tree-control="my_tree" drag-enabled="true" column-defs="[]"
            expand-on="expanding_property" on-select="my_tree_handler(branch)" on-click="my_tree_handler(branch)"
            template-url="tree-table-template-render.html"></tree-table>
```


## Add attributes
    * enable-move: `true`: To move node, `false`: to copy node *(default `true`)*
    * enable-hotkey: `true`: press 'shift' to move node, unpress 'shift' to copy node. *(default `false`)*
    * enable-drag: to Enable-drag *(default `true`)*
    * enable-status: to show status moving, copying *(default `false`)*
    * template-copy: to add url template of `Status Copy` *(can bypass string or variable in controller, but just only get $templateCache, if not exist will get default)*;
    * template-move: to add url template of `Status Move` *(can bypass string or variable in controller, but just only get $templateCache, if not exist will get default)*;
    * Example:
```html
<tree-table class="tree-table table table-hover b-b b-light" tree-data="tree_data" tree-control="my_tree"
    primary-key="primaryKey" 
    callbacks="callbacks" 
    
    enable-drag="true"
    enable-status="true" 
    enable-move="true" 
    icon-leaf="none" 
    icon-expand="fa fa-fw fa-angle-right"
    icon-collapse="fa fa-fw fa-angle-down" 
    
    column-defs="col_defs" 
    expand-on="expanding_property"
    
    template-url="tree-table-template.html" 
    template-move="'tree-table-template.html'"
    template-copy="tree-table-template.html"

    data-indent="30"
    data-indent-unit="px"
    data-indent-plus="15"
></tree-table>

```