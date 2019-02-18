---
layout: post
title: Tabbed Lists Widget
subtitle: Widget to show multiple data tables in tabs
tags: [widgets]
comments: true
---

This is a widget that you can use to show multiple data tables lists on your service portal, it comes OOTB with 3 tabs, but could easily be extendable and configurable to include more tabs.

<b>Installation Steps</b>

Client Script

Configure the tab names

Server Script

Configure the tables, fields ,filters and pages that open on list click.

Portal

Add it to any page.


## Screenshot

![Tabbed List Widget](/img/tabbed-list-widget.JPG)

# HTML Template
{% highlight html %}
<div class="vms-status-tabs">
  <div class="three-tabs row">
    <div class="all-tabs">
      <div class="each-tab" ng-click="c.selectedTab = 'one'" ng-class="{'active': c.selectedTab === 'one'}">        
        <p class="name">{{::c.tabs.firstTab.name}}</p>
      </div>
      <div class="each-tab" ng-click="c.selectedTab = 'two'" ng-class="{'active': c.selectedTab === 'two'}">        
        <p class="name">{{::c.tabs.secondTab.name}}</p>
      </div>
      <div class="each-tab" ng-click="c.selectedTab = 'three'" ng-class="{'active': c.selectedTab === 'three'}">        
        <p class="name">{{::c.tabs.thirdTab.name}}</p>
      </div>
    </div>
    <div class="tab-details">
      <div ng-show="c.selectedTab === 'one'">
        <div class="tab-details fade" ng-class-even="'light-grey-bg'" ng-class="{'in': c.selectedTab === 'one'}">
          <sp-widget widget="data.listWidget1"></sp-widget>
        </div>
      </div>
      <div ng-show="c.selectedTab === 'two'">
        <div class="tab-details fade" ng-class-even="'light-grey-bg'" ng-class="{'in': c.selectedTab === 'two'}">
          <sp-widget widget="data.listWidget2"></sp-widget>
        </div>
      </div>
      <div ng-show="c.selectedTab === 'three'">
        <div class="tab-details fade" ng-class-even="'light-grey-bg'" ng-class="{'in': c.selectedTab === 'three'}">
          <sp-widget widget="data.listWidget3"></sp-widget>
        </div>
      </div>
    </div>
  </div>
</div>
{% endhighlight %}

# CSS
{% highlight css %}
.panel-heading > .panel-title{font-size: 28px;font-family: SourceSansPro;color: #FFF;font-weight: lighter;margin-left:15px}.glyphicon-menu-hamburger{display:none !important}.inactive{cursor: not-allowed;pointer-events: none}.pd{margin:15px}.badge{font-weight:200 !important;font-size: 14px !important}.fade.ng-enter{transition:0.5s linear all;opacity:0}.fade.ng-enter.ng-enter-active{opacity:1}.vms-status-tabs{margin-bottom:20px;.small-text{font-size: 10px;color: #595959;text-transform: uppercase}.light-grey-bg{background: #f9f9f9}.people-card-media{padding-right: 10px;padding-left: 10px;display: flex;justify-content: flex-start;align-items: center;float: left;margin: 0px;.flex-it-align-top{display: flex;justify-content: space-between}.media-left{.list__photo{width: 35px;height: 35px}.initials-badge{min-width: 36px;float: left;margin-top: 0px;border-radius: 50%;line-height: 1;font-size: 14px;margin-left: 0px;padding: 10px 10px;font-weight: 200;background: #AEB2B3}}}.three-tabs{.orange{color: #e89033}.green{color: #70be5a}.red{color: #ff4000}.bg-green{background: #70be5a !important}.bg-red{background: #ff4000 !important}.bg-orange{background: #e89033}.bg-primary{background: #7889ba}.all-tabs{display: flex;padding: 0px;.each-tab{width: 33.3%;float: left;text-align: center;padding: 20px 10px;background: #fff;//border-bottom: 1px solid #e4e5e6;&.active{background:#428bca;border-left: 0px;border-bottom: 0px;border-right: 0px;//border-bottom: 1px solid #7889ba;.name{color: #FFF}}p{margin-bottom: 0px}.name{font-size: 16px;font-weight: normal;color: #909090;text-transform: uppercase;//background: #fbfbfb}.fa{color: #b4b2b3}}}.tab-details{padding: 10px;justify-content: space-between;align-items: center;.flex-it{display: flex;align-items: center;justify-content: space-around}.svg-icon{height: 23px;width: 27px;fill: #9C9C9C}p{margin-left: 5px;margin-bottom: 0px}.detail-numbers{font-size: 20px;font-weight:200;color: #595959}}}}
{% endhighlight %}

# Client Script
{% highlight javascript linenos %}
function ($scope, spUtil, $location, spAriaFocusManager, $timeout) {
	var c = this;
	$scope.$on('data_table.click', function(e, parms){
			var p = $scope.data.page_id || 'support_ticket' ;
			var s = {id: p, table: parms.table, sys_id: parms.sys_id, view: 'sp'};
			var newURL = $location.search(s);
			spAriaFocusManager.navigateToLink(newURL.url());
			$scope.$destroy();
	});

	c.$onInit = function() {
		c.selectedTab = 'one';
	};


	c.tabs = {
		firstTab: {
			name: '${TAB 1 NAME}'
		},
		secondTab: {
			name: '${TAB 2 NAME}'
		},
		thirdTab: {
			name: '${TAB 3 NAME}'
		}
	};
}
{% endhighlight %}

# Server Script
{% highlight javascript linenos %}
(function() {
 /*  "use strict"; - linter issues */
 // populate the 'data' object
 var sp_page = $sp.getValue('sp_page');
 var pageGR = new GlideRecord('sp_page');
 var limit = options.max_entries || 5;

 data.hasItilRole = gs.getUser().hasRole('itil');
 pageGR.get(sp_page);
 data.page_id = pageGR.getValue("id");

 $sp.getValues(data);
 if (data.field_list) {
  data.fields_array = data.field_list.split(',');
 } else {
  data.field_list = $sp.getListColumns(data.table);
 }

 if (input) {
  data.p = input.p;
  data.o = input.o;
  data.d = input.d;
  data.q = input.q;
 }
 data.p = data.p || 1;
 data.o = data.o || $sp.getValue('order_by');
 data.d = data.d || $sp.getValue('order_direction');

 data.page_index = (data.p - 1);
 data.window_size = $sp.getValue('maximum_entries') || 10;
 data.window_start = (data.page_index * data.window_size);
 data.window_end = (((data.page_index + 1) * data.window_size));
 data.filter = $sp.getParameter('filter') || $sp.getValue("filter");

 var gr = new GlideRecordSecure(data.table);
 if (!gr.isValid()) {
  data.invalid_table = true;
  data.table_label = data.table;
  return;
 }
 data.table_label = gr.getLabel();

 var widget1Params = {
  table: 'TABLE',
  fields: "FIELD1,FIELD2,FIELD3",
  o: data.o,
  d: data.d,
  filter: "FILTER_ENCODED_QUERY",
  hide_header: true,
  enable_filter: true,
  color: 'primary',
  window_size: data.window_size,
  view: 'sp',
  headerTitle: options.title,
  page: 'PAGE_ON_CLICK',
  show_breadcrumbs: true
 };

 var widget2Params = {
  table: 'TABLE',
  fields: "FIELD1,FIELD2,FIELD3",
  o: data.o,
  d: data.d,
  filter: "FILTER_ENCODED_QUERY",
  hide_header: true,
  enable_filter: true,
  color: 'primary',
  window_size: data.window_size,
  view: 'sp',
  headerTitle: options.title,
  page: 'PAGE_ON_CLICK',
  show_breadcrumbs: true
 };

 var widget3Params = {
   table: 'TABLE',
  fields: "FIELD1,FIELD2,FIELD3",
  o: data.o,
  d: data.d,
  filter: "FILTER_ENCODED_QUERY",
  hide_header: true,
  enable_filter: true,
  color: 'primary',
  window_size: data.window_size,
  view: 'sp',
  headerTitle: options.title,
  page: 'PAGE_ON_CLICK',
  show_breadcrumbs: true
 };

 data.listWidget1 = $sp.getWidget('widget-data-table', widget1Params);
 data.listWidget2 = $sp.getWidget('widget-data-table', widget2Params);
 data.listWidget3 = $sp.getWidget('widget-data-table', widget3Params);
})();
{% endhighlight %}
