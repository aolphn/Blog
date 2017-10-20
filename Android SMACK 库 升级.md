


### 遇到的问题

> Unexpected end of ZLIB input stream

setCompressionEnabled(true)的时候压缩存在问题，暂时关闭压缩

> jks keystore not found的问题

.setKeystoreType(null)，将keystore类型设置为null

> org.jivesoftware.smack.SmackException: Parser got END_DOCUMENT event. This could happen e.g. if the server closed the connection without sending a closing stream element

解决：https://discourse.igniterealtime.org/t/smack-4-2-alpha1-disconnecting-from-server-after-a-minute-smackexception-parser-got-end-document-event/62373 ，.setSendPresence(true)

> //delay消息携带的错误的时间戳stamp="20170831T07:12:06"smack解析失败，会导致链接断掉

ProviderManager.removeExtensionProvider("x","jabber:x:delay");将这个providere移除掉




 ### Changes from Smack 3.4 to 4.0

- Renamed Connection to XMPPConnection, which is subclassed by XMPPTCPConnection and XMPPBOSHConnection. You can use find -type f -name "*.java" |xargs sed -i 's;\([^A-Za-z]\)Connection\([^A-Za-z]\);\1XMPPConnection\2;g' to rename Connection to XMPPConnection in your java code.
- Moved provider and packages Classes to their respective package, i.e. org.jivesoftware..(package|provider) find -type f -name "*.java" |xargs sed -i 's;smackx.ServiceDiscoveryManager;smackx.disco.ServiceDiscoveryManager;' find -type f -name "*.java" |xargs sed -i 's;smackx.packet.VCard;smackx.vcardtemp.packet.VCard;'
- The keep-alive mechanism is now provided by PingManager and moved from core to extensions.
- PrivacyList: toString has been reanmed to getName
- You have to call Chat.close() before you drop all references to the Chat instance, or you will be leaking the Chat object until the ChatManager instance is gc'ed.
- ServerTrustManager has been removed, if you want to use XMPP over SSL you may need to provide your own SSLContext to ConnectionConfiguration
- Packet.setProperty() and co where moved out of smack-core into smack-extensions and the API is can now be found under org.jivesoftware.smackx.jiveproperties.
- Connection.getAccountManager() is now AccountManager.getInstance(XMPPConnection)
- All public API methods now return some kind of Collection instead of an Iterator.
- 
 ### SMACK 4.1

Smack 4.1 release highlights include support for XEP-198: Stream Management (SMACK-333) and native support for Android. Smack 4.1 obsoletes and is the legitimate successor of aSmack.

- XMPPConnection is now an interface. Use either AbstractXMPPConnection or one of its subclasses as static type when declaring the connection
- ==SASL authentication was reworked, if you use custom SASL mechanisms then you may have to adopt your code==
- IQ request handler API added. Packet listeners and collectors are no longer able to listen for IQ requests, i.e. IQs of type 'get' or 'set'.
- XMPPConnection.addPacketListener is deprecated: use either addAsyncPacketListener or addSyncPacketListener and their remove counterparts
- StringUtils is now to be found as XmppStringUtils in jxmpp-core (which Smack 4.1 automatically pulls in). The method names changed a bit, e.g. parseName became parseLocalpart and so on.
- AccountManager is now in smackx, since its specification is no longer part of the XMPP RFC(s), but instead a XEP. Use find . -type f -name '*.java' |xargs sed -i 's/import org.jivesoftware.smack.AccountManager;/import org.jivesoftware.smackx.iqregister.AccountManager;/' to change your source code accordingly.
- MessageListener has been renamed to ChatMessageListener. Attention: MessageListener still exists with a different interface contract!
- ConnectionConfiguration uses the builder pattern
- FormField.getType and Form.getType became Enums. Watch out for broken comparisons like formField.getType().equals("hidden")
- ==Packet became a deprecated interface. Use the new Stanza class and getStanzaId instead of getPacketID==
- Roster now follows the Manager pattern (use Roster.getInstanceFor(XMPPConnection) to obtain an instance, - - XMPPConnection.getRoster() is no longer available)
- ConnectionListener.authenticated adds a new boolean parameter resumed
- XMPPTCPConnection.addStanzaAcknowledgedListener does not throw StreamManagementNotEnabledException anymore - use XMPPTCPConnection.isSmEnabled
- Use ConnectionConfiguration.Builder.allowEmptyOrNullUsernames if you use SASL EXTERNAL
- SmackConfiguration.DEBUG_ENABLED became SmackConfiguration.DEBUG

 ### SMACK 4.2

#### Notable changes and important new features

Experimental support for DNSSEC

Experimental support for DNSSEC was added. For more information, have a look at the documentation.

#### Network operations became interruptible

Methods invoking network operations and awaiting responses are now interruptible. This means you can interrupt the thread performing them in case they are take too long to complete.

#### Use JXMPP's JID types

==JXMPP's JID types provide an powerful yet simply to use abstraction over an XMPP address (also called "JID"). This helps to minimize problems caused by using the wrong JID type or by the normalization applied to JIDs. To create a JID from a CharSequence (this includes String) simply use JidCreate.from(charSequence).==

#### Improved the Multi User Chat API

The MUC API was improved allowing easy creation of rooms and joining them.

#### Support for SASL authzid

Smack now supports SASL authzid.

#### API Changes

Warning: This list may not be complete

Introduced ConnectionConfiguration.setHostAddress(InetAddress)

In previous versions of Smack, ConnectionConfiguration.setHost(String) could be used to set the XMPP service's host IP address. This is no longer possible due to the added DNSSEC support. You have to use the new connection configuration ConnectionConfiguration.setHostAddress(InetAddress) instead.

#### Removed loginAnonymously()

Use ConnectionConfiguration.performSaslAnonymousAuthentication() instead. Then simply call the zero argument variant of login().

#### Further API additions/changes/removals and improvements

- Rewrote the PEP API to use the existing PubSub API
- Renamed processPacket(Stanza) to processStanza(Stanza)
- Renamed PacketCollector to StanzaCollector


Smack configuration with experimental extensions (XEP-0280, XEP-0352, XEP-0332, XEP-0335)

> ### XEP-0280 : Message Carbons ，多终端消息转发
> ### XEP-0352 : Client State Indication,在线状态
> ### XEP-0335 : JSON Containers
> ### XEP-0332 : HTTP over XMPP transport(https://xmpp.org/extensions/xep-0332.html)











