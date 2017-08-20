# jQuery-within-the-ASP.NET-UpdatePanel
Working with jQuery within the ASP.NET UpdatePanel

## Getting Started

While developing an ASP.NET Webforms application, I've found that I couldn't use JQuery alongside UpdatePanel ! I've looked deeply into it and found that the partial PostBack of UpdatePanel was removing the JQuery Events. Then I've made numerous attempts to make it working the UpdatePanel alongside JQuery ! I've googled a lot and reviewed a lot's of blog post but was unable to find the exact output from there !! 

Lastly I've found a solution to it by myself!

To illustrate the procedure, I've made a demo project which has two fully functional pages. In Page1.aspx I've tried to add two numbers using JQuery and server side events. On the button '+' I've added two numbers from JQuery and on the button '+(*)' I've added two numbers from server side event. While the page1.aspx is loading, the JQuery event binding was occurred in document.Ready.

The partial postback prevents the jquery to work. In this solution I've overcome the problem. I've solved it by the endRequest event which is raised after an asynchronous postback is finished and control has been returned to the browser.  

### Using the code

In the project you will have two pages one is for jQuery not Working in Update Panel (Page1.aspx) and another is jQuery Working in Update Panel (Page2.aspx).

In Page1.aspx jquery doesn't work.

In the + button will add two numbers using jquery and +(*) button will add two numbers using client side method. Here is the Page1.aspx code : 
```
<%@ Page Language="C#" AutoEventWireup="true" 

    CodeBehind="Page1.aspx.cs" Inherits="jQuerywithinUpdatePanel.Page1" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 

     "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>Page-1</title>

    <script src="js/jquery-1.7.2.min.js" 

         type="text/javascript"></script>

    <script type="text/javascript">
        //
        function IsValidNumber() {
            if ($(this).val() == "") {
                //$(this).val("0");
                alert("Please enter valid value!");
                $(this).focus();
            }
            else if ($.isNumeric($(this).val()) == false) {
                alert("Please enter valid value!");
                $(this).focus();
            }
        }

        function Add() {
            var Num1 = parseInt($('#txtNum1').val());
            var Num2 = parseInt($('#txtNum2').val());
            var Result = Num1 + Num2;
            $('#txtResult').val(Result);
        }

        $(document).ready(function() {
            $('#txtNum1').change(IsValidNumber);
            $('#txtNum2').change(IsValidNumber);
            $('#btnClientAdd').click(Add);
        });
    </script>

</head>
<body>
    <form id="form1" runat="server">
    <asp:ScriptManager ID="SM" runat="server">
    </asp:ScriptManager>
    <asp:UpdatePanel ID="upMain" runat="server" UpdateMode="Conditional">
        <ContentTemplate>
            <div>
                <table>
                    <tr>
                        <td>
                            <input type="button" id="btnClientAdd" value=" + " />
                        </td>
                        <td>
                            <asp:TextBox ID="txtNum1" 

                                runat="server" Width="100px"></asp:TextBox>
                            +
                            <asp:TextBox ID="txtNum2" 

                                runat="server" Width="100px"></asp:TextBox>
                            =
                            <asp:TextBox ID="txtResult" 

                                 runat="server" Width="100px"></asp:TextBox>
                        </td>
                        <td>
                            <asp:Button ID="btnServerAdd" runat="server" 

                                 Text=" +(*) " OnClick="btnServerAdd_Click" />
                        </td>
                    </tr>
                </table>
            </div>
        </ContentTemplate>
    </asp:UpdatePanel>
    </form>
</body>
</html>
```
and Page1.aspx.cs code:  
```
using System;
using System.Collections;
using System.Configuration;
using System.Data;
using System.Linq;
using System.Web;
using System.Web.Security;
using System.Web.UI;
using System.Web.UI.HtmlControls;
using System.Web.UI.WebControls;
using System.Web.UI.WebControls.WebParts;
using System.Xml.Linq;

namespace jQuerywithinUpdatePanel
{
    public partial class Page1 : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void btnServerAdd_Click(object sender, EventArgs e)
        {
            int Num1 = Convert.ToInt16(txtNum1.Text);
            int Num2 = Convert.ToInt16(txtNum2.Text);
            int Result = Num1 + Num2;
            txtResult.Text = Result.ToString();
        }
    }
} 
```

Here, the two numbers are being added from JQuery every time I click on button '+'. But after clicking on server side event on button '+(*)' once, the JQuery event doesn't fire ! That was the problem to me !

The solution I've shown in Page2.aspx.

In Page2.aspx, I've used one more function to complete the JQuery event binding which works after the partial PostBack is occurred and binds the JQuery events on the document.Ready on pageEndRequest. 


