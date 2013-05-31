---
layout: post
title: "ReorderList demo for AjaxControlToolkit"
date: 2013-05-30 15:55
comments: true
categories: 
- programming
- c#
- asp.net
---
[AjaxControlToolkit's](http://www.asp.net/ajaxLibrary/AjaxControlToolkitSampleSite/Default.aspx) 
[ReorderList](http://www.asp.net/ajaxLibrary/AjaxControlToolkitSampleSite/ReorderList/ReorderList.aspx) 
provides drag and drop functionality within a list.

``` html ReorderListDemo.aspx https://github.com/draptik/actdemos/blob/master/AjaxControlToolkitDemos/AjaxControlToolkitDemos/Pages/ReorderListDemo.aspx Source Article
<div class="CssReorderList">
    <!-- ClientMode="AutoID" is required for certain versions of AjaxControlToolkit  -->
    <ajaxToolkit:ReorderList ID="MyReorderList" runat="server"
                             DataKeyField="MyId"
                             SortOrderField="MyPosition"
                             PostBackOnReorder="False"
                             ClientIDMode="AutoID"
                             DragHandleAlignment="Left" 
                             ItemInsertLocation="Beginning"
                             AllowReorder="true"
        >
        <ItemTemplate>
            <div style="background-color: yellow;" class="CssItemArea">
                <asp:HiddenField runat="server" ID="hdfMyId" 
                	Value="<%# ((DummyViewModel)Container.DataItem).MyId %>" />
                <asp:Label runat="server" ID="lblName" 
                	Text="<%# ((DummyViewModel)Container.DataItem).MyName %>"/>
                <asp:Label runat="server" ID="lblPosition" 
                	Text="<%# ((DummyViewModel)Container.DataItem).MyPosition %>"/>
            </div>
        </ItemTemplate>
        <DragHandleTemplate>
            <div class="CssDragHandle"><strong>DRAG ME</strong></div>
        </DragHandleTemplate>
    </ajaxToolkit:ReorderList>
<div>    
```
I've placed a working example project on [github](https://github.com/draptik/actdemos).
