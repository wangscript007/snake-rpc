snake-rpc
=====

An RPC framework based on Hessian(http://hessian.caucho.com)

the name 'snake' comes from the Animal Year of snake in Chinese(the year 2013).

compared with hessian, snake-rpc has several new features:

* ablity to serialize InputStream/OutputStream;
* unlimited InputStream/OutputStream parameters;
* ablity to reuse remote objects returned by a method call;

for example:

    Connection con = client.getConnection();
    ResultSet rs = con.queryForResultSet("select * from users");

this feature is very useful for those objects which are valid only on the server-side, e.g, the Connection or ResultSet objects;

see https://github.com/bluejoe2008/snake-rpc/blob/master/test/cn/bluejoe/snake/client/SnakeClientTest.java for example usage.

###server side codes
a SnakeServlet class is provided, users can create derived classes:

		public class MySnakeServlet extends SnakeServlet
		{
		
			@Override
			public void init(ServletConfig config) throws ServletException
			{
				super.init(config);
				//declares that FileObjects are only avaliable on server side
				_skeleton.declareServerSideObjectClass(FileObject.class);
				//register an object named file
				_skeleton.registerServiceObject("file", new FileObjectImpl(new File("./testdir")));
			}
		
		}
		
###client side codes
* creates a SnakeClient first:

		HttpHost hc = new HttpHost("http://localhost:8080");
		_client = new SnakeClient(hc, "http://localhost:8080/rpc", "", "");

* uses createServiceObjectProxy() to retrieve remote object:

		FileObject fo = (FileObject) _client.createServiceObjectProxy("file", FileObject.class);
		
* now you got the FileObject object, just call methods on it:

		FileObject[] cfs = fo.listFiles();
		Assert.assertEquals(1, cfs.length);
		FileObject cfs0 = cfs[0];
		Assert.assertEquals(false, cfs0.isDirectory());
		Assert.assertEquals(f1.length(), cfs0.length());

