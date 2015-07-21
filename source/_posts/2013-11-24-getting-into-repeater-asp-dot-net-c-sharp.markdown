---
layout: post
title: "Getting into a Repeater in ASP.NET & C#"
date: 2013-11-24 15:01:51 -0700
comments: true
categories: 
- Web Development
tags:
- snippets
- asp.net
- c#
---
Sometimes ASP.NET and web forms drive me crazy.  For all the time savings they offer in some areas, it is downright ridiculous how hard simple tasks can be.  Here is one way you can get to other Repeater controls from a button click inside the Repeater.

```xml Basic Repeater with delete button for each Repeater item
<table>
  <asp:Repeater ID="rptPeople" runat="server">
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
  </asp:Repeater>
</table>
```

Now on a Delete button click, we want to remove the row (Repeater item, technically) and update our database.  So our OnClick event needs to retrieve our Label text so we can use that ID for updates.

The easiest way to do this is to use what sent you there (i.e. your button) to get to the parent Repeater item and from there find the specific Label that holds our PersonId.[^1]

```csharp OnClick event for Delete button
protected void btnDeletePerson_Click(object sender, EventArgs e)
{
    Button btnDeletePerson = (Button)sender;
    RepeaterItem item = (RepeaterItem)btnDeletePerson.NamingContainer;
    Label lblPersonId = (Label)item.FindControl("lblPersonId");
}
```

There's also a shorthand way to do this with more casting; I like the first example as it's easy to tell what you're doing at each step, but once you've got the concept down the single line of this second example is very convenient.[^2]

```csharp FindControl shorthand
Label lblPersonId = (Label)((Control)sender).Parent.FindControl("lblPersonId");
```

And once we've got our hands on that PersonId, we can delete to our heart's content!

Sources:
[^1]: [stackoverflow: Find Control in asp:repeater on button click event](http://stackoverflow.com/a/10880370/1657324)
[^2]: [codeproject: In-place Editing with ASP.NET Repeater](http://www.codeproject.com/Articles/247289/In-place-editing-with-ASP-NET-Repeater)