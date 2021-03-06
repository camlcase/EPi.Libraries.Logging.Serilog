﻿NOTE: You will need a Logger configuration. You can either use my "EPi.Libraries.Logging.Serilog.AppSettings" package, so you can configure Serilog in the AppSettings
or you can create your own implementation.

    [ServiceConfiguration(ServiceType = typeof(ILoggerConfigurator), Lifecycle = ServiceInstanceScope.Singleton)]
    public class LoggerConfigurator : ILoggerConfigurator
    {
        public ILogger GetLogger()
        {
            return your own configuration here;
        }
    }

NOTE: As only one logging implementation can be used, you will need to remove "EPiServer.Logging.Log4Net" 

To do contextual logging create a Serilog.ILogger with the context of the passed in name. Like so:

private ILogger _logger;
protected ILogger GetLogger(string name)
{
     var logger = _logger ?? (_logger = new LoggerConfiguration().ReadFrom.AppSettings().Enrich.FromLogContext().CreateLogger());

     return string.IsNullOrWhiteSpace(name) ? logger : logger.ForContext("Logger", name);
}

This name can be resolved by Structuremap, for contextual logging you can pass in the ParentType:

container.Configure(x => x.For<ILogger>().AlwaysUnique().Use(s => LogManager.GetLogger(s.ParentType ?? typeof(ILogger))));

(Thanx Wessel Terpstra for this addition)

