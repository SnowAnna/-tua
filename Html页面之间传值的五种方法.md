一、QueryString传值：
1. 这是最简单的传值方式，但缺点是传的值会显示在浏览器的地址栏中且不能传递对象，只适用于传递简单的且安全性要求不高的整数值，例如：
2. 新建一个WEB项目，添加一个页面命名为Test1，在页面中添加一个Button命名为btnLogin，再添加两个TextBox分别命名为txtUserName和txtPassWord，添加Button的Click()事件：

private void btnLogin_Click (object sender, System.EventArgs e)

{

string url=" Test1.aspx?UserName=" +txtUserName.Text + "&Password=”+txtPassWord.Text+””;

Response.Redirect(url);

}

3. 添加另一个页面命名为Test2，在页面添加两个Lable分别命名为lblUserName和lblPassWord，添加页面的Load()事件：

private void Page_Load (object sender, System.EventArgs e)

{
    lblUserName.Text=Request.QueryString["UserName"];

lblPassWord.Text=Request.QueryString["Password"];

}

4. 把Test1设为起始页，运行项目在Test1页面的文本框中输入值后点击按钮，就可以在Test2页面中显Test1页面输入的结果。

 

二、Server.Transfer传值：
1. 这种方式避免了要传递的值显示在浏览器的地址栏中，但是比较麻烦，例如：

2. 新建一个WEB项目，添加两个页面分别命名为Test1和Test2，在Test1页面中添加一个Button命名为btnLogin，再添加两个TextBox分别命名为txtUserName和txtPassWord，在Test2页面添加两个Lable分别命名为lblUserName和lblPassWord，为Test1添加过程返回txtUserName和txtPassWord的值并添加btnLogin的Click()事件：

public string UserName

{

get

{

return txtUserName.Text;

}

}

public string Password

{

get

{

return txtPassWord.Text;

}

}

private void btnLogin_Click (object sender, System.EventArgs e)

{

Server.Transfer("Test2.aspx");

}
3. 添加Test2页面的Load()事件：

private void Page_Load (object sender, System.EventArgs e)

{

Test1 t1; //创建原始窗体的实例

t1=( Test1)Context.Handler; //获得实例化的句柄

lblUserName.Text= t1.UserName;

lblPassWord.Text= t1.Password;

}

4. 把Test1设为起始页，运行项目在Test1页面的文本框中输入值后点击按钮，就可以在Test2页面中显Test1页面输入的结果。

 

三、Cookie对象变量：
1. Cookie是针对每一个用户而言的，是存放在客户端的 ，Cookie的使用要配合ASP.NET内置对象Request来使用，例如：

2. 新建一个WEB项目，添加两个页面分别命名为Test1和Test2，在Test1页面中添加一个Button命名为btnLogin，再添加两个TextBox分别命名为txtUserName和txtPassWord，在Test2页面添加两个Lable分别命名为lblUserName和lblPassWord，为Test1添加Button的Click()事件：

private void btnLogin_Click (object sender, System.EventArgs e)

{

HttpCookie cookie_UserName = new HttpCookie("UserName");

HttpCookie cookie_PassWord = new HttpCookie("PassWord ");

cookie_ UserName.Value = txtUserName.Text;

cookie_ PassWord.Value = txtPassWord.Text;

Response.AppendCookie(cookie_ UserName);

Response.AppendCookie(cookie_ PassWord);

Server.Transfer("Test2.aspx");

}

3. 添加Test2页面的Load()事件：

private void Page_Load (object sender, System.EventArgs e)

{

lblUserName.Text = Request.Cookies["UserName"].Value.ToString();

lblPassWord.Text = Request.Cookies["PassWord "].Value.ToString();

}

4. 把Test1设为起始页，运行项目在Test1页面的文本框中输入值后点击按钮，就可以在Test2页面中显Test1页面输入的结果。

 

四、Session对象变量：
1. Session也是针对每一个用户而言的，是存放在服务器端的 ，Session不仅可以把值传递到下一个页面，还可以交叉传递到多个页面，直至把Session变量的值removed 后，变量才会消失，例如：

2. 新建一个WEB项目，添加两个页面分别命名为Test1和Test2，在Test1页面中添加一个Button命名为btnLogin，再添加两个TextBox分别命名为txtUserName和txtPassWord，在Test2页面添加两个Lable分别命名为lblUserName和lblPassWord，为Test1添加Button的Click()事件：

private void btnLogin_Click (object sender, System.EventArgs e)

{

Session["UserName"]=txtUserName.Text;

Session["PassWord"]=txtPassWord.Text;

Response.Redirect("Test2.aspx");

}

3. 添加Test2页面的Load()事件：

private void Page_Load (object sender, System.EventArgs e)

{

lblUserName.Text=Session["UserName"].ToString();

lblPassWord.Text=Session["Password"].ToString();

Session.Remove("UserName"); //清除Session

Session.Remove("PassWord"); //清除Session

}

4. 把Test1设为起始页，运行项目在Test1页面的文本框中输入值后点击按钮，就可以在Test2页面中显Test1页面输入的结果。

 

五、Application对象变量：
1. Application对象的作用范围是整个全局，也就是说对所有用户都有效。其常用的方法用Lock和UnLock，例如：

2. 新建一个WEB项目，添加两个页面分别命名为Test1和Test2，在Test1页面中添加一个Button命名为btnLogin，再添加两个TextBox分别命名为txtUserName和txtPassWord，在Test2页面添加两个Lable分别命名为lblUserName和lblPassWord，为Test1添加Button的Click()事件：

private void btnLogin_Click (object sender, System.EventArgs e)

{

Application["UserName"] = txtUserName.Text;

Application["PassWord "] = txtPassWord.Text;

Server.Transfer("Test2.aspx");

}

3. 添加Test2页面的Load()事件：

private void Page_Load (object sender, System.EventArgs e)

{

Application.Lock();

lblUserName. Text = Application["UserName"].ToString();

lblPassWord. Text = Application["PassWord "].ToString();

Application.UnLock();

}

4. 把Test1设为起始页，运行项目在Test1页面的文本框中输入值后点击按钮，就可以在Test2页面中显Test1页面输入的结果。