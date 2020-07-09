# xuni-wenshidu
用模拟器来模拟温湿度并且把采集到的数据传入到C#里面，并且用波形图展现出来，并且把数据存入数据库，以及用web界面来连接数据库来做到实时展示温湿度

# 模拟虚拟环境，实现物联网温湿度采集，实现智能农业，这里是涉及到C#知识的，要改的东西很简单，先把里面的数据库连接改成你自己数据库连接字符串，然后再把wpf2里面引用的demo3_4那个文件删掉，然后自己再重新生成一个新的，再进行添加，因为如果不这样做，路径会出错的，里面有一些注意事项，需要修改的地方在，web.config这个文件还有，mainwindow这里而已，不过存储用户数据这块我没有完善，属于自己去完善，这里给出参考代码，还有数据库的名字必须和我的一样，否则还有一些地方要进行修改



```c#
using System;
using System.Collections.Generic;
using System.Data.SqlClient;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Shapes;

namespace test628
{
    /// <summary>
    /// Login.xaml 的交互逻辑
    /// </summary>
    public partial class Login : Window
    {
        public Login()
        {
            InitializeComponent();
        }
        //用户名
        private string UserName = "";
        //用户密码
        private string UserPwd = "";
        //用户性别
        private string UserGender = "";
        //用户籍贯
        private string UserOriginName = "";
        //用户爱好
        private string UserHobbies = "";
        //用户建议
        private string UserSuggest = "";
        #region Welcome

        private void grdWelcome_MouseDown_1(object sender, MouseButtonEventArgs e)
        {
            GridShow(grdLogin);
        }
        #endregion
        #region Registered

        private void btnRegisteredCancel_Click_1(object sender, RoutedEventArgs e)
        {
            GridShow(grdLogin);
            txtRegisteredUserName.Text = "";
            pwdRegisteredUserPwd1.Password = "";
            pwdRegisteredUserPwd2.Password = "";
        }

        private void btnRegistered_Click_1(object sender, RoutedEventArgs e)
        {
            //信息完整检测
            if (txtRegisteredUserName.Text == "" ||
                pwdRegisteredUserPwd1.Password.ToString() == "" ||
                pwdRegisteredUserPwd2.Password.ToString() == "")
            {
                MessageBox.Show("请填写完整！", "提示", MessageBoxButton.OK, MessageBoxImage.Information);
                return;
            }

            //两次密码一致性检测
            if (pwdRegisteredUserPwd1.Password.ToString() !=
                pwdRegisteredUserPwd2.Password.ToString())
            {
                MessageBox.Show("两次密码不一致,请重新输入！", "提示", MessageBoxButton.OK, MessageBoxImage.Information);
                return;
            }

            //注册成功===========================
            //用户名
            UserName = txtRegisteredUserName.Text;
            //密码
            UserPwd = pwdRegisteredUserPwd1.Password.ToString();
            //性别
            if (rbtnBoy.IsChecked != null)
            {
                UserGender = (((bool)rbtnBoy.IsChecked) ? "男" : "女");
            }
            else
            {
                UserGender = "女";
            }
            //创建数据库连接
            //声明数据库连接变量
            string connString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=MySchool;Integrated Security=True";
            //数据库查值调用
            string sql = String.Format("INSERT INTO Users(UserName,Password,Sex) VALUES('{0}','{1}',N'{2}')", UserName, UserPwd, UserGender);
            //创建数据库连接
            try
            {
                using (SqlConnection conn = new SqlConnection(connString))
                {
                    conn.Open();//打开数据库
                    SqlCommand comm = new SqlCommand(sql, conn);//创建Command对象
                    int n = comm.ExecuteNonQuery();//执行添加命令，返回值为更新的行数
                    if (n > 0)
                    {
                        MessageBox.Show("数据保存成功！！！", "提示", MessageBoxButton.OK, MessageBoxImage.Information);
                    }
                    else
                    {
                        MessageBox.Show("数据保存失败！！！", "提示", MessageBoxButton.OK, MessageBoxImage.Error);
                    }

                }
            }
            catch (Exception ex)
            {

                MessageBox.Show(ex.Message, "异常信息", MessageBoxButton.OK, MessageBoxImage.Error);
            }


            GridShow(grdLogin);
            txtRegisteredUserName.Text = "";
            pwdRegisteredUserPwd1.Password = "";
            pwdRegisteredUserPwd2.Password = "";
        }
        /// <summary>
        /// 籍贯类型选择事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>

        #endregion
        #region Login

        private void btnRegisteredShow_Click_1(object sender, RoutedEventArgs e)
        {
            GridShow(grdRegistered);
            txtLoginUserName.Text = "";
            pwdLoginUserPwd.Password = "";
        }

        private void btnLogin_Click_1(object sender, RoutedEventArgs e)
        {
            string userName = txtLoginUserName.Text;
            string password = pwdLoginUserPwd.Password;
            //声明数据库连接变量
            string connString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=MySchool;Integrated Security=True";
            //数据库查值调用
            string sql = String.Format("select count(*) from [Users] where UserName='{0}'and Password='{1}'", userName, password);
            //创建数据库连接
            try
            {
                using (SqlConnection conn = new SqlConnection(connString))
                {
                    conn.Open();//打开数据库
                    SqlCommand comm = new SqlCommand(sql, conn);//创建Command对象
                    int n = (int)comm.ExecuteScalar();//执行查询语句,返回匹配的行数
                    //if (UserPwd == "" && UserName == "")
                    //{//未注册
                    //    Msg += ",请进行注册操作！";
                    //}
                    if (txtLoginUserName.Text == "" || pwdLoginUserPwd.Password == "")
                    {
                        MessageBox.Show("请在输入框内正确输入账号密码！！！", "警告", MessageBoxButton.OK, MessageBoxImage.Warning);
                    }
                    else
                    {
                        if (n == 1)
                        {

                            GridShow(grdWelcome);
                        }
                        else
                        {
                            MessageBox.Show("密码错误！！！", "警告", MessageBoxButton.OK, MessageBoxImage.Warning);
                        }

                    }



                }
            }
            catch (Exception ex)
            {

                MessageBox.Show(ex.Message, "操作数据库出错！", MessageBoxButton.OK, MessageBoxImage.Error);
            }
        }
        #endregion
        private void GridShow(Grid grd)
        {
            grdRegistered.Visibility = Visibility.Hidden;
            grdLogin.Visibility = Visibility.Hidden;
            grdWelcome.Visibility = Visibility.Hidden;
            grd.Visibility = Visibility.Visible;
        }

        private void Window_Loaded_1(object sender, RoutedEventArgs e)
        {
            GridShow(grdLogin);
        }
        private void Btn_web_click(object sender, RoutedEventArgs e)
        {
            MainWindow mainwindow = new MainWindow();
            mainwindow.Show();
            this.Hide();
        }


    }
}
```

