# `gcr.io/paketo-buildpacks/apache-tomee`
The Apache Tomee Buildpack is a Cloud Native Buildpack that contributes Apache Tomee and Process Types for WARs.

## Behavior
This buildpack will participate all the following conditions are met

* `<APPLICATION_ROOT>/WEB-INF` exists
* `Main-Class` is NOT defined in the manifest
* `BP_JAVA_APP_SERVER` is set to `tomee`

The buildpack will do the following:

* Requests that a JRE be installed
* Contribute a Tomee instance to `$CATALINA_HOME`
* Contribute a Tomee instance to `$CATALINA_BASE`
  * Contribute `context.xml`, `logging.properties`, `server.xml`, and `web.xml` to `conf/`
  * Contribute [Access Logging Support][als], [Lifecycle Support][lcs], and [Logging Support][lgs]
  * Contribute external configuration if available
* Contributes `tomee`, `task`, and `web` process types

### Tiny Stack

When this buildpack runs on the [Tiny stack](https://paketo.io/docs/concepts/stacks/#tiny), which has no shell, the following notes apply:
* As there is no shell, the `catalina.sh` script cannot be used to start Tomee
* The Tomee Buildpack will generate a start command directly. It does not support all the functionality in `catalina.sh`.
* Some configuration options such as `bin/setenv.sh` and setting `CATALINA_*` environment variables, will not be available.
* Tomee will be run with `umask` set to `0022` instead of the `catalina.sh`provided default of `0027`

[als]: https://github.com/cloudfoundry/java-buildpack-support/tree/master/tomcat-access-logging-support
[lcs]: https://github.com/cloudfoundry/java-buildpack-support/tree/master/tomcat-lifecycle-support
[lgs]: https://github.com/cloudfoundry/java-buildpack-support/tree/master/tomcat-logging-support

## Configuration
| Environment Variable | Description
| -------------------- | -----------
| `$BP_TOMEE_CONTEXT_PATH` | The context path to mount the application at.  Defaults to empty (`ROOT`).
| `$BP_TOMEE_EXT_CONF_SHA256` | The SHA256 hash of the external configuration package
| `$BP_TOMEE_EXT_CONF_STRIP` | The number of directory levels to strip from the external configuration package.  Defaults to `0`.
| `$BP_TOMEE_EXT_CONF_URI` | The download URI of the external configuration package
| `$BP_TOMEE_EXT_CONF_VERSION` | The version of the external configuration package
| `$BP_TOMEE_VERSION` |  Configure a specific Tomee version.  This value must _exactly_ match a version available in the buildpack so typically it would configured to a wildcard such as `9.*`.
| `$BP_TOMEE_DISTRIBUTION` |  Configure a specific Tomee distribution.  This value must be one of `microprofile`, `webprofile`, `plus` or `plume`. Defaults to `microprofile`.
| `$BPL_TOMEE_ACCESS_LOGGING_ENABLED` | Whether access logging should be activated.  Defaults to inactive.
| `$BPL_TOMEE_ENV_*` | Variables to be converted to system properties.
| `$BPL_TOMEE_BINDING_NAME` | Name of the binding to convert into system properties.

### External Configuration Package
The artifacts that the repository provides must be in TAR format and must follow the Tomee archive structure:

```
<CATALINA_BASE>
└── conf
    ├── context.xml
    ├── server.xml
    ├── web.xml
    ├── ...
```

### Dynamic System Properties

Tomee supports property replacement in its [configuration files](https://tomcat.apache.org/tomcat-9.0-doc/config/systemprops.html). These system properties can be configured in two ways:

  1. Use of `BPL_TOMEE_ENV_*` environment variables at launch time.
  2. Configured from a binding with `BPL_TOMEE_BINDING_NAME`.

## Bindings
The buildpack optionally accepts the following bindings:

### Type: `dependency-mapping`
|Key                   | Value   | Description
|----------------------|---------|------------
|`<dependency-digest>` | `<uri>` | If needed, the buildpack will fetch the dependency with digest `<dependency-digest>` from `<uri>`

## License
This buildpack is released under version 2.0 of the [Apache License][a].

[a]: http://www.apache.org/licenses/LICENSE-2.0

