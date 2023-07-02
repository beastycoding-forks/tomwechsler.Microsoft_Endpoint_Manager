https://docs.microsoft.com/en-us/mem/intune/protect/certificates-scep-configure

1. Register certificate (the NDES certificate) in the Personal Store

2. Install the NDES service

	a. In the Wizard, select Active Directory Certificate Services to gain access to the AD CS Role Services. 
	   Select Network Device Enrollment Service, uncheck Certification Authority, and then complete the wizard.

	b. When NDES is added to the server, the wizard also installs IIS. Confirm that IIS has the following configurations:

		Web Server > Security > Request Filtering

		Web Server > Application Development > ASP.NET 3.5

		Installing ASP.NET 3.5 installs .NET Framework 3.5. When installing .NET Framework 3.5, 
		install both the core .NET Framework 3.5 feature and HTTP Activation.

		Web Server > Application Development > ASP.NET 4.7.2

		Installing ASP.NET 4.7.2 installs .NET Framework 4.7.2. When installing .NET Framework 4.7.2, 
		install the core .NET Framework 4.7.2 feature, ASP.NET 4.7.2, and the WCF Services > HTTP Activation feature.

		Management Tools > IIS 6 Management Compatibility > IIS 6 Metabase Compatibility

		Management Tools > IIS 6 Management Compatibility > IIS 6 WMI Compatibility

	
3. On the server, add the NDES service account as a member of the local IIS_IUSR group.

4. Create the SPN in a command prompt with elevated privileges: setspn –s http/ndes.tomsmem.local tomsmem\ndes

5. Configure the NDES service:

    In Role Services, select the Network Device Enrollment Service.  
    In Service Account for NDES, specify the NDES Service Account.  
    In CA for NDES, click Select, and then select the issuing CA where you configured the certificate template.  
    In Cryptography for NDES, set the key length to meet your company requirements.  
    In Confirmation, select Configure to complete the wizard.  

6. After the wizard completes, update the following registry key on the computer that hosts the NDES service:

   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\MSCEP\

	Signature CorpIntuneBenutzer  
	Encryption CorpIntuneBenutzer  
	Signature and encryption CorpIntuneBenutzer  

7. Install and bind certificates on the server that hosts NDES:

    After installing the server authentication certificate, open IIS Manager, and select the Default Web Site.  
    In the Actions pane, select Bindings.  
    Select Add, set Type to https, and then confirm the port is 443.  
    For SSL certificate, specify the server authentication certificate.  

    On the NDES Server open IIS Manager, select the Default Web Site in the Connections pane, and then open Request Filtering.  
	
    Click Edit Feature Settings, and then set the following:  
    query string (Bytes) = 65534  
    Maximum URL length (Bytes) = 65534  

8. Add the NDES account in the local security policies under "local policies" at "assign user rights" at "log on as service"!
	
9. Restart the server that hosts the NDES service. Don't use iisreset; iireset doesn't complete the required changes.

10. For example, go to the following URL on your DC, you should see the NDES default page.  
    https://ndes.tomsmem.local/certsrv/mscep/mscep.dll