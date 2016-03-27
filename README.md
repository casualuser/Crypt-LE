# Crypt-LE

This module provides the functionality necessary to use Let's Encrypt API and generate free SSL certificates for your domains. It can also be used to generate private RSA keys or Certificate Signing Requests without resorting to openssl command line. Crypt::LE is shipped with a self-sufficient client for obtaining SSL certificates - `le.pl`. 

> The client + library package is codenamed ZeroSSL with the project homepage at https://ZeroSSL.com

### INSTALLATION

**With CPANminus**

    cpanm Crypt::LE
    
**With CPAN**

    cpan -i Crypt::LE
    
**Manual installation**:

	perl Makefile.PL
	make
	make test
	make install

### CLIENT

With `le.pl` you should be able to quickly get your SSL certificates issued. Run it without parameters to see how it is used. The client supports 'http' and 'dns' challenges out of the box.

> **Important:** By default all your actions are run against the test server, which behaves exactly as the live one, but produces certificates **not** trusted by the browsers. Once you have tested the process and want to get an actual **trusted** certificate, always append **`--live`** parameter to the command line.

*_Usage example:_*

    le.pl --key account.key --csr domain.csr --csr-key domain.key --crt domain.crt --domains  "www.domain.ext,domain.ext" --generate-missing

That will generate an account key and a CSR if they are missing. If any of those files exists, they will just be loaded, so it is safe to re-run the client. 

*_To use HTTP verification and have challenge files created/removed automatically, you can use `--path` and `--unlink` parameters:_*

    le.pl ... --path /some/path/to/.well-known/acme-challenge --unlink

*_To use DNS verification of domain ownership, you can use `--handle-as` and `handle-with` parameters:_*

     le.pl ... --handle-as dns --handle-with Crypt::LE::Challenge::Simple

For more examples, logging configuration and all available parameters overview use `--help`:

    le.pl --help

**Note:** It is advised to also use `--email` parameter for the very first run of the client, to register your account key with the email. While it is optional, that will allow you to receive certificaties expiration notifications and it might be used later to recover access to your account if you lose the key.

### RENEWALS

To RENEW your existing certificate use the same command line as you used for issuing the certificate, with one additional parameter:

       --renew XX, where XX is the number of days left until certificate expiration.

If client detects that it is XX or fewer days left until certificate expiration, then (and only then) the renewal process will be run, so the script can be safely put into crontab to run on a daily basis if needed.

The amount of days left is checked by either of two methods:

 * If the certificate (which name is used with --crt parameter) is available locally, then it will be loaded and checked.
 * If the certificate is not available locally (for example if you moved it to another server), then an attempt to connect to the domains listed in --domains or CSR will be made until the first successful response is received. The peer certificate will be then checked for expiration.

### PLUGINS

Both the library and the client can be easily extended with custom plugins to handle Let's Encrypt challenges (both pre- and post-verification). See *Crypt::LE::Challenge::Simple* module as an example of such plugin. 

The client application can also be easily extended with modules handling process completion. See *Crypt::LE::Complete::Simple* module as an example of such plugin.

Client options related to plugins are:

 * --handle-with
 * --handle-params
 * --handle-as
 * --complete-with
 * --complete-params

Please note that parameters for `--handle-params` and `--complete-params` are expected to be valid JSON documents or to point to files containing valid JSON documents.

### CUSTOM LOGGING

Client uses *Log::Log4perl* module for logging. You can easily configure it to log into file, database, syslog, etc. Logger object is available to plugins which are called from the client and to the library itself. Below is an example of a logging configuration file to log both to screen and into le.log file:

     log4perl.rootLogger=DEBUG, File, Screen
     log4perl.appender.File = Log::Log4perl::Appender::File
     log4perl.appender.File.filename = le.log
     log4perl.appender.File.mode = append
     log4perl.appender.File.layout = PatternLayout
     log4perl.appender.File.layout.ConversionPattern = %d [%p] %m%n
     log4perl.appender.Screen = Log::Log4perl::Appender::Screen
     log4perl.appender.Screen.layout = PatternLayout
     log4perl.appender.Screen.layout.ConversionPattern = %d [%p] %m%n

Save the configuration into some file and then run `le.pl` with `--log-config` parameter specifying that configuration file name, for those settings to take effect.

### SUPPORT AND DOCUMENTATION

After installing, you can find documentation for this module with the
perldoc command.

    perldoc Crypt::LE

You can also look for information at:

 * [RT, CPAN's request tracker (report bugs here)](http://rt.cpan.org/NoAuth/Bugs.html?Dist=Crypt-LE)
 * [AnnoCPAN, Annotated CPAN documentation](http://annocpan.org/dist/Crypt-LE)
 
For feedback or custom development requests see:

 * Company homepage - https://do-know.com
 * Project homepage - https://ZeroSSL.com

### LICENSE AND COPYRIGHT

Copyright (C) 2016 Alexander Yezhov

This program is free software; you can redistribute it and/or modify it
under the terms of the the Artistic License (2.0). You may obtain a
copy of the full license at:

http://www.perlfoundation.org/artistic_license_2_0

Any use, modification, and distribution of the Standard or Modified
Versions is governed by this Artistic License. By using, modifying or
distributing the Package, you accept this license. Do not use, modify,
or distribute the Package, if you do not accept this license.

If your Modified Version has been derived from a Modified Version made
by someone other than you, you are nevertheless required to ensure that
your Modified Version complies with the requirements of this license.

This license does not grant you the right to use any trademark, service
mark, tradename, or logo of the Copyright Holder.

This license includes the non-exclusive, worldwide, free-of-charge
patent license to make, have made, use, offer to sell, sell, import and
otherwise transfer the Package with respect to any patent claims
licensable by the Copyright Holder that are necessarily infringed by the
Package. If you institute patent litigation (including a cross-claim or
counterclaim) against any party alleging that the Package constitutes
direct or contributory patent infringement, then this Artistic License
to you shall terminate on the date that such litigation is filed.

Disclaimer of Warranty: THE PACKAGE IS PROVIDED BY THE COPYRIGHT HOLDER
AND CONTRIBUTORS "AS IS' AND WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES.
THE IMPLIED WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR
PURPOSE, OR NON-INFRINGEMENT ARE DISCLAIMED TO THE EXTENT PERMITTED BY
YOUR LOCAL LAW. UNLESS REQUIRED BY LAW, NO COPYRIGHT HOLDER OR
CONTRIBUTOR WILL BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, OR
CONSEQUENTIAL DAMAGES ARISING IN ANY WAY OUT OF THE USE OF THE PACKAGE,
EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
