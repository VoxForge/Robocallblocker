# Robocallblocker
Configurations for OBI110 and FreePBX/Asterisk block landline robocallers.
 
Found an old OBI110 I had purchased a few years ago and finally got around to setting it up to communicate with FreePBX on an old HP mini netbook.  Can now send predictive dialers to Spam voice mail box, and send whitelist to our landline phones.

**OBIHAI OBI110**

*PHONE Port*

	Enable			
	DigitMap	        default		
	([1-9]x?*(Mpli)|[1-9]|[1-9][0-9]|911|**0|***|#|**1(Msp1)|**2(Msp2)|**8(Mli)|**9(Mpp)|(Mpli))
	OutboundCallRoute	default		
	{([1-9]x?*(Mpli)):pp},{(<#:>|911):li},{**0:aa},{***:aa2},{(<**1:>(Msp1)):sp1},{(<**2:>(Msp2)):sp2},{(<**8:>(Mli)):li},{(<**9:>(Mpp)):pp},{(Mpli):pli}
	CallReturnDigitMaps	default		
	{pli:(xx.)},{sp1:(<**1>xx.)},{sp2:(<**2>xx.)},{li:(<**8>xx.)},{pp:(<**9>xx.)}
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
	(1xxxxxxxxxx|<1>[2-9]xxxxxxxxx|011xx.|xx.|(Mipd)|[^*#]@@.)
	
*SIP*

	ProxyServer	192.168.1.13
	ProxyServerPort	6060
	RegistrarServerPort 6060
	OutboundProxyPort 6060
	X_UseRefer - selected
	X_AccessList - selected
	X_MWISubscribe - selected



