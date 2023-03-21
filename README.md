# easysecsgem
Packaged from secs4net, making it easier for everyone to use the secs/gem protocol
# version
Corresponds to the version of secs4net
# use
# interface

<table>
  <tr>
    <th>Interface</th>
    <th>Method</th>
    <th>Desc</th>
  </tr>
  <tr>
    <td>IHsmsConnStateHandler</td>
    <td>connectionChanged</td>
    <td>Connection status notification</td>
  </tr>
  <tr>
    <td>IHsmsMessageProcess</td>
    <td>receiveMessage</td>
    <td>Message notification and conduct business</td>
  </tr>
  <tr>
    <td>ILogMessage</td>
    <td>message、messageIn、messageOut</td>
    <td>log</td>
  </tr>
</table>

# dependence
Secs4Net      2.1.1

Secs4Net.Sml  2.1.1

Secs4Net.Json 2.1.1

# example c#

<code>
namespace EasySecsGemTest
{
    public partial class Form1 : Form,IHsmsConnStateHandler, IHsmsMessageProcess, ILogMessage
    {
        public Form1()
        {
            InitializeComponent();
        }

        private HsmsConn hsmsConn = null;
        private void Form1_Load(object sender, EventArgs e)
        {
            bool activeMode = true;
            hsmsConn = HsmsManager.getConnection(activeMode,"127.0.0.1", 5000, 65533, 1);
            hsmsConn.setMessageHandler(this);
            hsmsConn.setConnStateHandler(this);
            hsmsConn.setLogger(new SimpleSecsLogger(this));
            hsmsConn.connect();
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="connectionState">Connecting,Connected,Selected...</param>
        public void connectionChanged(Secs4Net.ConnectionState connectionState)
        {
            Console.WriteLine("connectionChanged:" + connectionState.ToString());
        }


        /// <summary>
        /// Receive messages and can conduct our business here
        /// </summary>
        /// <param name="messageWrapper"></param>
        async void IHsmsMessageProcess.receiveMessage(PrimaryMessageWrapper messageWrapper)
        {
            if (messageWrapper.PrimaryMessage.S == 1 && messageWrapper.PrimaryMessage.F == 1)
            {
                await messageWrapper.TryReplyAsync(new SecsMessage(1, 2, false));
            }
        }


        /// <summary>
        /// Log
        /// </summary>
        /// <param name="info">log level</param>
        /// <param name="message">msg</param>
        public void message(string info, string message)
        {
            Console.WriteLine($"{info}:{message}");
        }

        /// <summary>
        /// Receive message trigger
        /// </summary>
        /// <param name="msg"></param>
        /// <param name="id"></param>
        public void messageIn(SecsMessage msg, int id)
        {
            Console.WriteLine("messageIn:" + msg.ToSml());
        }

        /// <summary>
        ///  send message trigger
        /// </summary>
        /// <param name="msg"></param>
        /// <param name="id"></param>
        public void messageOut(SecsMessage msg, int id)
        {
            Console.WriteLine("messageOut:" + msg.ToSml());
        }

    }
}
</code>
