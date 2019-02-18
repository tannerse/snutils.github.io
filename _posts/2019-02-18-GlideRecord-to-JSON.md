---
layout: post
title: GlideRecord to JSON
subtitle: Script Include to retrieve record and return a JSON string
tags: [snippets]
comments: true
---

This is a helper script include to return entire GlideRecord as a JSON string that supports server side or client side scripting using GlideAjax. 

## Script Include

{% highlight javascript linenos %}
var GlideRecordAjaxUtils = Class.create();
GlideRecordAjaxUtils.prototype = Object.extendsObject(AbstractAjaxProcessor, {

	getRecord: function(table,sys_id,fields){
		table = table || this.getParameter('sysparm_table');
		sys_id = sys_id || this.getParameter('sysparm_sys_id');
		fields = fields || this.getParameter('sysparm_fields');

		var gr = new GlideRecord(table);
		gr.get(sys_id);

		return this._convertGRtoJSON(gr, fields);

	},

	_convertGRtoJSON : function(record, objFields) {
		var fields = new GlideRecordUtil().getFields(record),
			resultObject = {},
			field;
		if(typeof objFields != 'undefined')
			fields = objFields.toString().split(',');
		for (field in fields) {
			resultObject[fields[field]] = (record.getValue(fields[field])+'')
				.replace(/\\n/g, "\\n")
				.replace(/\\'/g, "\\'")
				.replace(/\\"/g, '\\"')
				.replace(/\\&/g, "\\&")
				.replace(/\\r/g, "\\r")
				.replace(/\\t/g, "\\t")
				.replace(/\\b/g, "\\b")
				.replace(/\\f/g, "\\f");
		}
		return JSON.stringify(resultObject);
	},

	type: 'GlideRecordAjaxUtils'
});
{% endhighlight %}

## Server Side

{% highlight javascript linenos %}
/* Return entire record */
new GlideRecordAjaxUtils().getRecord('TABLE_NAME','RECORD_SYS_ID')

/* Return specific fields */
new GlideRecordAjaxUtils().getRecord('TABLE_NAME','RECORD_SYS_ID', ["sys_id","number","short_description"])
{% endhighlight %}

## Client Side
{% highlight javascript linenos %}
var ga = new GlideAjax('GlideRecordAjaxUtils');
ga.addParam('sysparm_name', 'getRecord');
ga.addParam('sysparm_table', 'TABLE_NAME');
ga.addParam('sysparm_sys_id', 'RECORD_SYS_ID');
/* Optional */
ga.addParam('sysparm_fields', ["FIELD_NAME1","FIELD_NAME2"]);
/* Optional */
ga.getXML(function(response){
    var responseDocument = response.responseXML.documentElement;
    var answer = responseDocument.getAttribute('answer');    
    console.log(answer);
});
{% endhighlight %}
