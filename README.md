# Robocallblocker (work in progress)
Configurations for OBI110 and FreePBX/Asterisk block landline robocallers.
 
We have an old-school landline phone for home use and have been getting many SPAM calls. This is an approach to blocking those calls using an OBI110 [ATA](https://en.wikipedia.org/wiki/Analog_telephone_adapter) and [FreePBX](https://en.wikipedia.org/wiki/FreePBX).  

It simply asks callers to press a key, and if they do, it then transfers the call to our regular [landline](https://en.wikipedia.org/wiki/Landline) phone.  If no one answers, it sends the call to a good voice mail extension.  If they don't a key, it sends the call to a SPAM voice mail.  A whitelist allows known callers bypass the IVR Spam filter altogether and ring your phone directly.

The ATA used in this document is an old OBI110 I had purchased a few years ago and finally got around to setting up to communicate with FreePBX on an old HP mini netbook. 

(Sources: [ObiTalk Forum: Using the Obi 110 as an FXO and FXS Port for FreePBX](http://www.obitalk.com/forum/index.php?topic=1157.msg7261#msg7261), [FreePBX Documentation wiki](https://wiki.freepbx.org/pages/viewpage.action?pageId=4161594))

***OBIHAI OBI110 - setting up basic communications between OBI110 and FreePBX***

This document assumes that you have your OBI110 connected to your network and telephone jack, and have installed [FreePBX 14 (with version 13 of Asterisk)](https://www.freepbx.org/downloads/) on a dedicated computer, which is connected to the same network as the OBI110.  

The OBI110 ip address is assumed to be 192.168.1.13, FreePBX is at 192.168.1.11, and your router is at 192.168.1.1.  Change this to reflect your network settings.  This document also assumes that your home phone number is 555-555-1234 - change this to your phone number.


**1. OBI110 Network Settings**

*Internet Settings*

	IPAddress	192.168.1.13 (requires static ip address)
	SubnetMask	255.255.255.0
	DefaultGateway	192.168.1.1 (ip address of your router)
	DNSServer1	192.168.1.1

---

***2. Configure Line Port***

Connection from wall telephone jack (rj11) to OBI110 to FreePBX [trunk](https://en.wikipedia.org/wiki/Trunking).  

The [RJ11 jack](https://en.wikipedia.org/wiki/Registered_jack) is connected via a standard telephone cable to OBI110.  The OBI110 is then connected to your home LAN using Ethernet cable so that it can connect to your FreePBX server.

**2.a. OBI110 Line Port**

Physical Interfaces > LINE Port

	Enable			selected	
	DigitMap	        default		
	InboundCallRoute	SP2(5555551234)	
	
(SP2 = SP2 Service (under Voice Services).  It puts your telephone number in the SIP2 Inbound route on the OBITrunk1.  SP2 is configured on the OBI110.  Trunk and associated Inbound route is configured on FreePBX server).

**2.b. ITSP Profile B**
(ITSP = Internet Service Provider - i.e. FreePBX)

*General*

	Name		Line to SP2 to FreePBX Trunk
	DigitMap	default

Service Provider Info - defaults

*SIP*

	ProxyServer	192.168.1.13 (ip address of FreePBX server)
	ProxyServerPort	5060 (default)
	RegistrarServerPort 5060 (default)
	OutboundProxyPort 5060 (default)
	X_SpoofCallerID - selected
	X_AccessList 	192.168.1.13 (only allow access to OBI110 from FreePBX server ip address)

FreePBX server port 5060 points to chan_sip on FreePBX in this configuration; (this is not a default FreePBX setting)

**2.c. Voice Services**

**SP2 Service**

	Enable			selected
	X_ServProvProfile	B (points to ITSP Profile B)
	X_InboundCallRoute	LI (LI = line = cable connected to telephone wall jack)
	X_KeepAliveServerPort	5060 (default)
	X_UserAgentPort		5061 (default)

**SIP Credentials**

	AuthUserName	OBITRUNK1 (used in FreePBX)
	AuthPassword	password (used in FreePBX)
	
**Calling Features**

	defaults

---

**2.d. FREEPBX Trunk**	

Connectivity > Trunks

	Trunk Name		OBITRUNK1
	Outbound CallerID 	5555551234 (phone number of line attache to OBI110)
	
	Dial Number Manipulation Rules - none
	
	sip Settings

		Outgoing
			Trunk Name	OBITRUNK1 (must match OBI110 SP2 configuration)
			PEER Details
				username=OBITRUNK1 (must match OBI110 SP2 configuration)
				secret=password (must match OBI110 SP2configuration)
				host=dynamic
				type=friend
				context=from-trunk
				qualify=yes
				dtmfmode=rfc2833
				canreinvite=no
				disallow=all
				allow=ulaw

		Incoming	(leave blank)

**2.e. FreePBX Inbound Route**

Connectivity > Inbound Routes

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

Applications > Extensions

	Extensions: 300
	General
		Display Name - myextension
		Secret (long password generated by FreePBX that will be used to configure the OBI phone, sip1 configurations)
		
		User Manager Settings - defaults
		(an extension, which can be accessed using numeric keypad of telephone set is also associated with a FreePBX username, see Admin>User Management)
	
	Voicemail
		Enabled - selected
		Voicemail Password 1234 (numeric password used to access voice mail from phone handset)
		Disable (*) in Voicemail Menu - No

	Find Me/Follow Me - defaults
	Advanced - defaults
	Pin Sets - defaults
	Other - defaults

**2.f OBIHAI OBI110 - test connection to FreePBX**

Status > System Status

scroll down to SP2 Service Status.  Should see something like this:

	Status		Registered (server=192.168.1.13:6060; expire in 46s)	help

---

***3. PHONE***

This sets up a communication link between your phone and FreePBX (i.e. a line on the OBITRUNK1)

**3.a. OBI110 PHONE Port**

Physical Interfaces > PHONE Port

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

**3.b. ITSP Profile A**
(ITSP = Internet Service Provider - i.e. FreePBX)

*General*

	Name		Phone to OBI110 to FreePBX Trunk
	DigitMap	default

*SIP*

	ProxyServer	192.168.1.13
	ProxyServerPort	6060 (nonstandard port)
	RegistrarServerPort 6060 (nonstandard port)
	OutboundProxyPort 6060 (nonstandard port)
	X_UseRefer - selected
	X_AccessList - selected
	X_MWISubscribe - selected (might not be required since not using sip phone)

(Port 6060 corresponds to non-standard port for Chan PJSIP Settings)

**3.c. Voice Services**

**SP1 Service**

	Enable			selected
	X_ServProvProfile	A (default - points to ITSP Profile A)
	X_InboundCallRoute	ph (default)
	X_KeepAliveServerPort	6060
	X_UserAgentPort		6060

(ph = phone)

**SIP Credentials**

	AuthUserName	300 (extension created earlier)
	AuthPassword	FREEPBXPASSWORD (long password generated by FreePBX when extension was created)

**Calling Features**

	MaxSessions	10
	MWIEnable	selected
	X_VMWIEnable	selected (should not be required since not using SIP phone)
	MessageWaiting	unselected, not default

**3.d. OBIHAI OBI110 - test connection to FreePBX**

Status > System Status

scroll down to SP1 Service Status.  Should see something like this:

	Status 		Registered (server=192.168.1.13:5060; expire in 34s)

---

**3.e. FREEPBX Outbound Routes**

Your landline phone now has a route to the FreePBX server.  

To actually connect to FreePBX from your phone (via OBI110) you need to dial '**1'.  You can do this now.  

From here you can dial 300 to get the extension we create and leave a voice mail. 

Or, if you want to call someone as you normally would on a telephone, we need to tell FreePBX how to do this.  One way is to create a dial code that tells FreePBX to send your keypresses to the PSTN (Public Service Telephone Network).  This is how we create that code.

Connectivity > Outbound Routes

	Route Settings
		Route: 81DialsPOTS
		Trunk Sequence for Matched Routes: OBITRUNK1
		
	Dial Patterns
	
		81|
		81|1NXXNXXXXXX
		81|NXXNXXXXXX
		81|NXXXXXX
		81|X11
		
	Import/Export Patterns - not applicable
	
	Additional Settings - defaults

(POTS = Plain Old Telephone Service = PSNT)

***4. Whitelisting***

The Inbound Route on FreePBX decides what happens when someone calls your phone.  As part of the initial set up, we told FreePBX to send the call to extension 300, and since your phone is linked to this extension through OBI110, any call coming in will ring on your landline phone.

What can we do to prevent robo calls from making your phone ring?  

Spam callers usually use a [predictive dialer](https://en.wikipedia.org/wiki/Predictive_dialer) to connect you to their spam agent.  What happens is that the Robocaller calls many nuumbers, and if someone answers, proceeds to try to match them with an available attendant.  But the robocallers are dumb, and if you ask them to press a digit, they cannot (not yet at least - voice spammers are beginning to use speech recogniion, more on that later...).

Therefore, to prevent roboCallers, you need require anyone calling you to dial 8 before allowing the call to go through.  We can use the FreePBX IVR app to do this.

**4.a Record caller prompts**

First, we need to record our the messages we want the caller to hear.

Using an Audio recording app (such as [Audacity](https://www.audacityteam.org/)), record the following phrase:

	Hello, you've reached 555 555 1234, please press 8 to continue

and save it as a wav file named:

	greetingAndPressToContinue.wav

(note: if your browser supports it, you can record your prompt in your browser when adding a new recording - see: Admin > System Recordings, then 'Record in Browser')

**4.b Upload recording to FreePBX**

Next upload your wavefile to FreePBX

	Admin > System Recordings

Add New System Recording

	Name			greetingAndPressToContinue.wav
	Description		greeting and press key to Continue
	Upload Recording

**4.c Setup IVR**

on FreePBX: Application > IVR

	IVR Name 		PressToContinue
	IVR Description		Press key in order to continue 
	Announcement		greetingAndPressToContinue.wav
	Enable Direct Dial 	Disabled
	Invalid Recording	Default
	Invalid Destination	Extensions: 400 spamExtension
	Timeout Retry Recording	Default
	Timeout Recording	Default
	Invalid Recording	Extensions: 400 spamExtension
	Return to IVR after VM	No
	IVR Entries
		Digits 		8
		Destination 	Extensions: 300 myextension (already created)
		return		No
		
Applications > Extensions

	Extensions: 400
	General
		Display Name - spamExtension
		Secret (long password generated by FreePBX that will be used to configure the OBI phone, sip1 configurations)
		
		User Manager Settings - defaults
	Voicemail
		Enabled - selected
		Voicemail Password 1234 (numeric password used to access voice mail from phone handset)
		Disable (*) in Voicemail Menu - Yes
		
	Find Me/Follow Me - defaults
	Advanced - defaults
	Pin Sets - defaults
	Other - defaults
