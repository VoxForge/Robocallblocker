# Robocallblocker
Configurations for OBI110 and FreePBX/Asterisk block landline robocallers.
 
Found an old OBI110 I had purchased a few years ago and finally got around to setting it up to communicate with FreePBX on an old HP mini netbook.  Can now send predictive dialers to Spam voice mail box, and send whitelist to our landline phones.

***OBIHAI OBI110***

**Network Settings**

*Internet Settings*

IPAddress
SubnetMask
DefaultGateway
DNSServer1

***PHONE***

**PHONE Port**

	Enable			selected		
	DigitMap	        default		
	OutboundCallRoute	default		
	CallReturnDigitMaps	default		
	PrimaryLine	        SP1 Service
		
Ringer - defaults

Port Settings - defaults

Calling Features

	StarCodeProfile     None

Timers - defaults

Tip-Ring Voltage Polarity  - defaults

	
**ITSP Profile A**
(Internet Service Provider - i.e. FreePBX)

*General*

	DigitMap	default

*SIP*

	ProxyServer	192.168.1.13
	ProxyServerPort	6060
	RegistrarServerPort 6060
	OutboundProxyPort 6060
	X_UseRefer - selected
	X_AccessList - selected
	X_MWISubscribe - selected

**Voice Services**

**SP1 Service**

	Enable			selected
	X_ServProvProfile	A (default)
	X_InboundCallRoute	ph (default)
	X_KeepAliveServerPort	6060
	X_UserAgentPort		6060

**SIP Credentials**

	AuthUserName	300
	AuthPassword	FREEPBXPASSWORD
	MWIEnable	selected
	MessageWaiting	unselected, not default

***FREEPBX***	

**Trunk**
Connectivity > Trunks

	
