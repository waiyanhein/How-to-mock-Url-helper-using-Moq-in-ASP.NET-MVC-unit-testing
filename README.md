# How-to-mock-Url-helper-using-Moq-in-ASP.NET-MVC-unit-testing
This page show you how to mock Url helper like Url.Content("Path"), Url.Action("Action","Controller",routeValue) and so on using Moq in ASP.NET MVC unit testing

###1. Create a BaseController that inherits from MVC Controller. All your controllers should inherit from this controller

```
public class BaseController : Controller
    {
        private IUrlHelper _urlHelper;

        public new IUrlHelper Url
        {
            get { return _urlHelper; }
            set { _urlHelper = value; }
        }
    }

    public interface IUrlHelper
    {
        string Action(string actionName);
        string Action(string actionName, object routeValues);
        string Action(string actionName, string controllerName);
        string Action(string actionName, RouteValueDictionary routeValues);
        string Action(string actionName, string controllerName, object routeValues);
        string Action(string actionName, string controllerName, RouteValueDictionary routeValues);
        string Action(string actionName, string controllerName, object routeValues, string protocol);
        string Action(string actionName, string controllerName, RouteValueDictionary routeValues, string protocol, string hostName);
        string Content(string contentPath);
        string Encode(string url);
        string RouteUrl(object routeValues);
        string RouteUrl(string routeName);
        string RouteUrl(RouteValueDictionary routeValues);
        string RouteUrl(string routeName, object routeValues);
        string RouteUrl(string routeName, RouteValueDictionary routeValues);
        string RouteUrl(string routeName, object routeValues, string protocol);
        string RouteUrl(string routeName, RouteValueDictionary routeValues, string protocol, string hostName);
    }

```

If you want to unit test an action using one of the methods of Url helper, below is the example. NOTE : Your controller must inherits from the BaseController above.
```
    public class ExampleController : BaseController
    {
        public string TestAction(string path)
        {
            return Url.Content("~/"+path);     
        }
    }
```

In the above controller, I will test TestAction method. I am just appending "~/" to the path parameter.

####This is how I simple code of testing above action method mocking Url helper using Moq

```
   [TestClass]
   public class TestClass
   {
       [TestMethod]
       public void TestActionMethod()
       {
          string path = "testing";
          string expected  = "~/"+path;
       
          Mock<IUrlHelper> urlHelperMock = new Mock<IUrlHelper>();
          urlHelperMock.Setup(x => x.Content(It.IsAny<string>())).Returns<string>(x => "~/" + x);// Mocj Content method of Url Helper
          
          ExampleController controller = new ExampleController();
          
          string result = controller.TestAction(path) as string;
          Assert.AreEqual(expected, result);
       }
   }
```

That's it. I hope you are clear with above Test class.
