# What is ConditionalFeature

## How do i get started?

Inherit one of the ConditionalFeatures; BooleanConditionalFeature or EnumConditionalFeature (more to come). Three approaches can be used with the FeatueToggles.

 - Set the value directly on instantiating, or override ResolveFeatureValue().
 - Provide a Func. This will be called every time the ConditionalFeature is queried. The Func controls the thread safety.
 - Provide a Lazy. This will be called only once, first time the ConditionalFeature is queried. The value will be cached for future queries. This one is thread safe.

 *Use only one of the listed approaches*
    
    // Using static value
    public class ShowDiagnosticsFeature : BooleanConditionalFeature
    {
        public ShowDiagnosticsFeature()
            : base(true)
        {}
    }

    var featureDiagnostics = new ShowDiagnosticsFeature();
    if( featureDiagnostics.IsEnabled ) {
        // do enabled stuff...
    }
    ...
    if( featureDiagnostics.IsDisabled ) {
        // do disabled stuff...
    }
    ...    
    featureDiagnostics.OnEnabled( () => {
        // do enabled stuff...
    });
    featureDiagnostics.OnDisabled( () => {
        // do enabled stuff...
    });

or

    // Using Func, will be evaluated every time the ConditionalFeature is queried
    public class GodUserFeature : BooleanConditionalFeature
    {
        public GodUserFeature(Func<bool> func)
            : base(func,false)
        {}
    }

    var godUser = new GodUserFeature(() => {
        var identity = Thread.CurrentPrincipal.Identity;
        return identity.IsAuthenticated && identity.Name.ToLowerInvariant() == "me";
    });
    if( godUser.IsEnabled ) {
        // do god stuff...
    }
    
    
or

    // Using Lazy, called once on first hit.    
    public class LicenseFeature : BooleanConditionalFeature
    {
        public LicenseFeature(Lazy<bool> lazy)
            : base(lazy,false)
        {}
    }

    var license = new LicenseFeature( new Lazy<bool>( () => {
        var licenseLines = File.ReadAllLines("license.txt")
        return licenseLines.Any(l=>l == "payed:true" );
    });
    if( license.IsEnabled ) {
        // Full throttle...
    }
    


Recommends use of DependencyInjection to request you ConditionalFeatures through out your application.


## Where can I get it?

`PM> Install-Package MicroKnights.ConditionalFeature`

See also Package `MicroKnights.ConditionalFeature.Configuration` (Soon comming) for using appsettings.config to configure the ConditionalFeatures.


# Test, not currently working
*Waiting for a Resharper Fix to run xUnit on dotnetcore v2.0 projects*

ConditionalFeature is Copyright © 2017 MicroKnights and is entirely free to use.