# Robocallblocker (work in progress as Feb 21)
Configurations for OBI110 and FreePBX/Asterisk block landline robocallers.
 
Found an old OBI110 I had purchased a few years ago and finally got around to setting it up to communicate with FreePBX on an old HP mini netbook.  Can now send predictive dialers to Spam voice mail box, and send whitelist to our landline phones.

(Sources: [FreePBX Documentation wiki](https://wiki.freepbx.org/pages/viewpage.action?pageId=4161594), [ObiTalk Forum: Using the Obi 110 as an FXO and FXS Port for FreePBX](http://www.obitalk.com/forum/index.php?topic=1157.msg7261#msg7261) )

***OBIHAI OBI110 - setting up basic communications between OBI110 and FreePBX***

**Network Settings**

*Internet Settings*

	IPAddress	192.168.1.13 (need a static ip address for OBI110)
	SubnetMask	255.255.255.0
	DefaultGateway	192.168.1.1 (ip address of your router)
	DNSServer1	192.168.1.1

---

***Line***

(The connection from OBI110 to rj11 telephone jack)

**Line Port**

	Enable			selected	
	DigitMap	        default		
	InboundCallRoute	SP2(5194743773)	

**ITSP Profile B**
(ITSP = Internet Service Provider - i.e. FreePBX)

*General*

	DigitMap	default

Service Provider Info - defaults

*SIP*

	ProxyServer	192.168.1.13 (ip address of FreePBX service)
	ProxyServerPort	5060 (default)
	RegistrarServerPort 5060 (default)
	OutboundProxyPort 5060 (default)
	X_SpoofCallerID - selected
	X_AccessList 	192.168.1.13

FreePBX server port 5060 points to chan_sip on FreePBX in this configuration; (this is not a default FreePBX setting)

**Voice Services**

**SP2 Service**

	Enable			selected
	X_ServProvProfile	B
	X_InboundCallRoute	LI
	X_KeepAliveServerPort	5060 (default)
	X_UserAgentPort		5061 (default)

**SIP Credentials**

	AuthUserName	OBITRUNK1
	AuthPassword	password
	
**Calling Features**

	defaults


***FREEPBX***	

**Trunk**
Connectivity > Trunks

	Trunk Name		OBITRUNK1
	Outbound CallerID 	5555551234 (phone number of line attache to OBI110)
	
	Dial Number Manipulation Rules none
	
	sip Settings

		Outgoing
			Trunk Name	OBITRUNK1
			PEER Details
				username=OBITRUNK1
				secret=password (password used for SP2 Service - Sip Credentials)
				host=dynamic
				type=friend
				context=from-trunk
				qualify=yes
				dtmfmode=rfc2833
				canreinvite=no
				disallow=all
				allow=ulaw

		Incoming	(leave blank)

**Inbound Routes**

Route: Obi110

	General
		Description	Obi110
		DID Number	5555551234 (phone number of line attache to OBI110)
		Set Destination 
			Extensions > Add new Extension
			
	Advanced - defaults
	Privacy - defaults
	Fax - defaults
	Other - defaults

Applications

	Extensions: 300
	General
		Display Name myextension
		Secret (long password that will be used to configure the OBI line, sip2 configurations)
		
		User Manager Settings - defaults
		(an extension, which can be accessed using numeric keypad of telephone set must be associated with a FreePBX username, which can be used to access the extension on-line)
	
	Voicemail
		Enabled - selected
		Voicemail Password 1234 (numeric password used to access voice mail from phone handset)

	Find Me/Follow Me - defaults
	Advanced - defaults
	Pin Sets - defaults
	Other - defaults

---

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

**Calling Features**

	defaults
