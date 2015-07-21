---
layout: post
title: "ASP.NET Repeaters and the ItemDataBound event"
date: 2013-12-10 01:54:36 -0700
comments: true
categories: 
- Web Development
tags:
- snippets
- asp.net
- c#
---
When I first started using Repeater controls in ASP.NET, I would only ever use ItemTemplate to define the specifics of each row.  After creating a few Repeaters and being forced to wrap html table elements around them, I started to think there must be a better way.  Eventually I stumbled upon examples online showing the use of HeaderTemplate and FooterTemplate for just this purpose.

```xml HeaderTemplate and FooterTemplate containing table elements
<asp:Repeater ID="rptPeople" runat="server" OnItemDataBound="rptPeople_ItemDataBound">
  <HeaderTemplate>
    <table>
      <tr>
        <th colspan="4">People List</th>
      </tr>
  </HeaderTemplate>
  <ItemTemplate>
    <tr>
      <td>
        <asp:Label ID="lblPersonId" runat="server" 
        Text='<%# Eval("PersonId") %>' />
      </td>
      <td>
        <asp:TextBox ID="txtLastName" runat="server" 
        Text='<%# Eval("LastName") %>'></asp:TextBox>
      </td>
      <td>
        <asp:TextBox ID="txtFirstName" runat="server" 
        Text='<%# Eval("FirstName") %>'></asp:TextBox>
      </td>
      <td>
        <asp:Button ID="btnDeletePerson" runat="server" Text="Delete" 
        OnClientClick="return confirm('Are you sure you want to delete?');" 
        OnClick="btnDeletePerson_Click" />
      </td>
    </tr>
  </ItemTemplate>
  <FooterTemplate>
    </table>
  </FooterTemplate>
</asp:Repeater>
```

That looks a lot better, but it also created unexpected problems in the ItemDataBound event.  I'd always assumed that ItemDataBound only fired in the ItemTemplate, as that is where you bind specific item data through each iteration of the Repeater (plus they both start with 'Item', obvious yes?). Little did I know that HeaderTemplate and FooterTemplate also fire the ItemDataBound event.  

So of course I added a HeaderTemplate and FooterTemplate to some legacy code and started receiving my favorite error "object reference not set to an instance of an object." The problem was the original ItemDataBound event was trying to find controls and was looking in the Header which did not having said controls (causing the error).

To fix this, you simply need to tell your code to only run when fired from the ItemTemplate[^1]:

```csharp if statement checks for ListItemTypes
protected void rptPeople_ItemDataBound(object sender, RepeaterItemEventArgs e)
{
    if (e.Item.ItemType == ListItemType.AlternatingItem || e.Item.ItemType == ListItemType.Item)
    {
      Label lblPersonId = (Label)((Control)sender).Parent.FindControl("lblPersonId");
    }
}
```

Sources: 
[^1]: [ASP.NET forum: Cannot find control in Repeaters ItemDataBound](http://forums.asp.net/t/1495510.aspx)