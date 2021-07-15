
  
/**
* @Name BadgesEverywhere
* @author DevilBro
* @authorId 278543574059057154
* @sürüm 1.7.9
* @Description Sohbet / Üye görüntüler Rozetler (Nitro, Hypesquad, vb ...)
* @invite Jx3TjNS
* @donate https://www.paypal.me/MircoWittrien
* @patreon https://www.patreon.com/MircoWittrien
* @web sitesi https://mwittrien.github.io/
* @source https://github.com/mwittrien/BetterDiscordAddons/tree/master/Plugins/BadgesEverywhere/
* @updateUrl https://mwittrien.github.io/BetterDiscordAddons/Plugins/BadgesEverywhere/BadgesEverywhere.plugin.js
 */

modül . ihracat  =  ( _  =>  {
	const  yapılandırma  =  {
		"bilgi" : {
			"name" : "RozetlerHer Yerde" ,
			"yazar" : "DevilBro" ,
			"sürüm" : "1.7.9" ,
			"description" : "Sohbet/Üye Listesinde Rozetleri (Nitro, Hypesquad, vb...) görüntüler"
		} ,
		"değişim günlüğü" : {
			"sabit" : {
				"Tıkla" : "Rozetler artık yalnızca bir Kullanıcı Pop-up'ı açtıktan sonra görünmüyor"
			}
		}
	} ;
	
	dönüş  ( pencere . Lightcord  ||  pencere . LightCord ) ? sınıf  {
		getName  ( )  { dönüş  yapılandırması . bilgi . isim ; }
		getAuthor  ( )  { dönüş  yapılandırması . bilgi . yazar ; }
		getVersion  ( )  { dönüş  yapılandırma . bilgi . sürüm ; }
		getDescription  ( )  { return  "LightCord kullanmayın!" ; }
		yük  ( )  { BdApi . alert ( "Dikkat!" ,  "LightCord'u kullanarak, 3. Taraf İstemci kullanmanız nedeniyle Discord Hesabınızı riske atıyorsunuz. Uygun BD Enjeksiyonuna sahip resmi bir Discord İstemcisine (https://discord.com/) geçin (https: //betterdiscord.app/)" ) ; }
		başlat ( )  { }
		dur ( )  { }
	} : ! pencere . BDFDB_Küresel  ||  ( ! pencere . BDFDB_Global . yüklendi  &&  ! pencere . BDFDB_Global . başlatıldı ) ? sınıf  {
		getName  ( )  { dönüş  yapılandırması . bilgi . isim ; }
		getAuthor  ( )  { dönüş  yapılandırması . bilgi . yazar ; }
		getVersion  ( )  { dönüş  yapılandırma . bilgi . sürüm ; }
		getDescription  ( )  { return  ` ${ yapılandırması için gereken Kitaplık Eklentisi . bilgi . } adı eksik. İndirmek için Eklenti Ayarlarını açın. \n\n ${ yapılandırma . bilgi . açıklama } ` ; }
		
		indirKütüphane  ( )  {
			gerektirir ( "istek" ) . get ( "https://mwittrien.github.io/BetterDiscordAddons/Library/0BDFDB.plugin.js" ,  ( e ,  r ,  b )  =>  {
				if  ( ! e  &&  b  &&  r . statusCode  ==  200 )  gerektirir ( "fs" ) . writeFile ( gerektirir ( "yol" ) . join ( BdApi . Plugins . folder ,  "0BDFDB.plugin.js" ) ,  b ,  _  =>  BdApi . showToast ( "BDFDB Kitaplığı indiriliyor" ,  { type : "success" }) ) ;
				başka  BdApi . uyarı ( "Hata" ,  "BDFDB Kitaplık Eklentisi indirilemedi. Daha sonra tekrar deneyin veya GitHub'dan manuel olarak indirin: https://mwittrien.github.io/downloader/?library" ) ;
			} ) ;
		}
		
		yük  ( )  {
			if  ( ! pencere . BDFDB_Global  ||  ! Array . isArray ( pencere . BDFDB_Global . pluginQueue ) )  penceresi . BDFDB_Global  =  Nesne . atama ( { } ,  pencere . BDFDB_Global ,  { pluginQueue : [ ] } ) ;
			if  ( ! pencere . BDFDB_Global . downloadModal )  {
				pencere . BDFDB_Global . downloadModal  =  doğru ;
				BdApi . showConfirmationModal ( "Kütüphane eksik" ,  `Kütüphane Eklentisi için gerekli {$ yapılandırma . bilgi . ad } eksik. it.` yüklemek için "Şimdi Yükle" tıklayın ,  {
					ConfirmText : "Şimdi İndir" ,
					iptalText : "İptal" ,
					onCancel : _  =>  { pencereyi sil  . BDFDB_Global . indirModal ; } ,
					onConfirm : _  =>  {
						 pencereyi sil . BDFDB_Global . indirModal ;
						bu . indirKütüphane ( ) ;
					}
				} ) ;
			}
			eğer  ( ! penceresi . BDFDB_Global . pluginQueue . içermektedir ( yapılandırma . bilgi . ad ) )  penceresi . BDFDB_Global . eklenti Sırası . itme ( yapılandırma . bilgi . ad ) ;
		}
		start  ( )  { bu . yük ( ) ; }
		dur  ( )  { }
		getSettingsPanel  ( )  {
			izin  şablonu  =  belge . createElement ( "şablon" ) ;
			şablon . innerHTML  =  `<div style="color: var(--header-primary); font-size: 16px; font-weight: 300; white-space: pre; line-height: 22px;">Kütüphane Eklentisi için gerekli ${ yapılandırma . bilgi . name } eksik.\nLütfen yüklemek için <a style="font-weight: 500;">Şimdi İndir</a>'i tıklayın.</div>` ;
			şablon . içerik . ilkElementChild . sorguSeçici ( "a" ) . addEventListener ( "click" ,  bu . downloadLibrary ) ;
			dönüş  şablonu . içerik . firstElementChild ;
		}
	} : ( ( [ Eklenti ,  BDFDB ] )  =>  {
		var  _this ;
		var  loadUsers  =  { } ,  QueuedInstances  =  { } ,  requestQueue  =  { kuyruk : [ ] ,  zaman aşımı : null ,  id : null } ,  cacheTimeout ;
		var  specialFlag ;
		
		Dönüş  sınıfı  BadgesEverywhere  uzanır  Tapa  {
			onLoad  ( )  {
				_bu  =  bu ;
				
				specialFlag  =  BDFDB . NumberUtils . createId ( )  +  "ÖZEL BAYRAK" ;
		
				bu . patchedModüller  =  {
					sonra : {
						MemberListItem : "render" ,
						MessageUsername : "varsayılan" ,
						UserProfileBadgeList : "varsayılan"
					}
				} ;
				
				bu . varsayılanlar  =  {
					yerler : {
						sohbet :				 { değer : doğru ,  	açıklama : "Sohbet" } ,
						üyeList :			 { değer : true ,  	açıklama : "Üye Listesi" }
					} ,
					rozetler : { } ,
					göstergeler : {
						CURRENT_GUILD_BOOST : { değer : doğru }
					}
				} ;
				
				için  ( let  anahtarı  ait  Nesne . tuşları ( BDFDB . LibraryComponents . UserBadgesKeys ) . Filtre ( n  =>  isNaN ( parseInt ( n ) ) ) )  {
					let  basicKey  =  anahtarı . değiştir ( / _LEVEL_ \d + / g ,  "" ) ;
					eğer  ( ! bu . varsayılan . rozetleri [ basicKey ] )  bu . varsayılan . rozetler [ basicKey ]  =  { değer : true ,  anahtarlar : [ ] } ;
					bu . varsayılan . rozetler [ basicKey ] . anahtarlar . itme ( BDFDB . LibraryComponents . UserBadgesKeys [ anahtar ] ) ;
				}
				
				bu . css  =  `
					${ BDFDB . dotCN . _badgeseverywhererozetleri } {
						ekran: satır içi esnek !önemli;
						haklı içerik: merkez;
						hizalama öğeleri: merkez;
						esnek sarma: nowrap;
						pozisyon: göreceli;
						kenar boşluğu: 0 0 0 4px;
						kullanıcı seçimi: yok;
					}
					${ BDFDB . dotCN . _badgeseverywhererozetleri } > * {
						kenar boşluğu: 0;
					}
					${ BDFDB . dotCN . _badgeseverywhererozetleri } > * + * {
						sol kenar boşluğu: 4 piksel;
					}
					${ BDFDB . dotCNS . _badgeseverywherebadges  +  BDFDB . dotCN . kullanıcı rozeti } {
						ekran: esnek;
						haklı içerik: merkez;
						hizalama öğeleri: merkez;
					}
					${ BDFDB . dotCNS . _badgeseverywherebadges  +  BDFDB . dotCN . kullanıcı rozeti  +  BDFDB . dotCN . _badgeseverywhereindicator } ::önce {
						görüntü yok;
					}
					${ BDFDB . dotCNS . _badgeseverywherebadgessettings  +  BDFDB . dotCN . kullanıcı rozeti } {
						genişlik: 24 piksel !önemli;
						yükseklik: 20 piksel !önemli;
					}
					${ BDFDB . dotCN . üyepremium simgesi } {
						görüntü yok;
					}
					${ BDFDB . dotCNS . _badgeseverywherebadges  +  BDFDB . dotCN . üyepremium simgesi } {
						Ekran bloğu;
						pozisyon: statik;
						kenar boşluğu: 0;
					}
					${ BDFDB . dotCN . _badgeseverywherebadgeschat } {
						pozisyon: göreceli;
						üst: 2 piksel;
					}
					${ BDFDB . dotCNS . messagerepliedmessage  +  BDFDB . dotCN . _badgeseverywherebadgeschat } {
						üst: 0;
					}
					${ BDFDB . dotCNS . mesaj  kompakt +  BDFDB . dotCN . mesajkullanıcıadı } ~ ${ BDFDB . dotCN . _badgeseverywhererozetleri } ,
					${ BDFDB . dotCNS . messagerepliedmessage  +  BDFDB . dotCN . mesajkullanıcıadı } ~ ${ BDFDB . dotCN . _badgeseverywhererozetleri } {
						kenar-sağ: .25rem;
						metin girintisi: 0;
					}
					${ BDFDB . dotCNS . messagerepliedmessage  +  BDFDB . dotCN . mesajkullanıcıadı } ~ ${ BDFDB . dotCN . _badgeseverywhererozetleri } {
						sol kenar boşluğu: 0;
					}
					
					${ BDFDB . dotCN . _badgeseverywherebadgessettings } {
						renk: var(--başlık-birincil);
					}
				` ;
			}
			
			onStart  ( )  {
				QueuedInstances  =  { } ,  loadUsers  =  { } ;
				requestQueue  =  { kuyruk : [ ] ,  zaman aşımı : null ,  id : null } ;
				
				let  badgeCache  =  BDFDB . DataUtils . yükle ( bu ,  "rozet Önbelleği" ) ;
				if  ( rozetÖnbelleği )  {
					hadi  şimdi  =  ( yeni  Tarih ( ) ) . getTime ( ) ,  ay  =  1000 * 60 * 60 * 24 * 30 ;
					için  ( let  id  içinde  badgeCache )  {
						if  ( şimdi  -  rozetCache [ kimlik ] . tarih  >  ay )  rozet Önbelleği [ kimlik ] silin  ;
						else  loadUsers [ id ]  =  rozetCache [ id ] ;
					}
					BDFDB . DataUtils . kaydet ( badgeCache ,  bu ,  "badgeCache" ) ;
				}
				
				const  processUser  =  ( id ,  data )  =>  {
					let  userCopy  =  Nesne . atama ( { } ,  veri . Kullanıcı ) ;
					userCopy . premium_since  =  veri . premium_since ;
					userCopy . premium_guild_since  =  veri . premium_guild_since ;
					loadUsers [ id ]  =  BDFDB . ObjectUtils . özü ( userCopy ,  "flags" ,  "premium_since" ,  "premium_guild_since" ) ;
					yüklenenKullanıcılar [ id ] . tarih  =  ( yeni  Tarih ( ) ) . getTime ( ) ;
					
					BDFDB . TimeUtils . temizle ( cacheTimeout ) ;
					cacheTimeout  =  BDFDB . TimeUtils . zaman aşımı ( _  =>  BDFDB . DataUtils . save ( loadedUsers ,  this ,  "badgeCache" ) ,  5000 ) ;
					
					if  ( requestQueue . id  &&  requestQueue . id  ==  id )  {
						BDFDB . ReactUtils . forceUpdate ( querydInstances [ requestQueue . id ] ) ;
						 sıraya alınmış Örnekleri sil [ requestQueue . kimlik ] ;
						requestQueue . kimlik  =  boş ;
						BDFDB . TimeUtils . zaman aşımı ( _  =>  bu . runQueue ( ) ,  1000 ) ;
					}
				} ;
				BDFDB . PatchUtils . yama ( bu ,  BDFDB . LibraryModules . DispatchApiUtils ,  "sevk" ,  { sonra : e  =>  {
					Eğer  ( BDFDB . ObjectUtils . olduğunu ( e . methodArguments [ 0 ] )  &&  e . methodArguments [ 0 ] . tip  ==  BDFDB . DiscordConstants . ActionTypes . USER_PROFILE_FETCH_FAILURE  &&  e . methodArguments [ 0 ] . userId )  {
						const  kullanıcı  =  BDFDB . Kütüphane Modülleri . Kullanıcı Mağazası . GetUser ( e . methodArguments [ 0 ] . userId ) ;
						processUser ( e . methodArguments [ 0 ] . userId ,  { kullanım : Kullanıcı  ||  { } ,  bayrakları : Kullanıcı ? kullanım . publicFlags : 0 } ) ;
					}
					Başka  halinde  ( BDFDB . ObjectUtils . olduğunu ( e . methodArguments [ 0 ] )  &&  e . methodArguments [ 0 ] . tip  ==  BDFDB . DiscordConstants . ActionTypes . USER_PROFILE_FETCH_SUCCESS  &&  e . methodArguments [ 0 ] . Kullanıcı )  processUser ( e . methodArguments [ 0] . kullanıcı . kimlik ,  e . methodArgümanlar [ 0 ] )
				} } ) ;

				bu . forceUpdateAll ( ) ;
			}
			
			onStop  ( )  {
				BDFDB . TimeUtils . temizle ( requestQueue . zaman aşımı ) ;

				bu . forceUpdateAll ( ) ;
			}

			getSettingsPanel  ( collapseStates  =  { } )  {
				 settingsPanel'e izin verin ;
				Dönüş  settingsPanel  =  BDFDB . PluginUtils . createSettingsPanel ( bu ,  {
					çöküşDurumları : çöküşDurumları ,
					çocuklar : _  =>  {
						let  settingsItems  =  [ ] ;
						
						ayarlarÖğeler . itin ( BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SettingsPanelList ,  {
							başlık : "Rozetleri şurada göster:" ,
							çocuklar : Nesne . tuşları ( bu . varsayılan . yerlerdir ) . map ( key  =>  BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SettingsSaveItem ,  {
								tip : "Anahtar" ,
								eklenti : bu ,
								tuşlar : [ "yerler" ,  tuş ] ,
								etiket : bu . varsayılan . yerler [ anahtar ] . açıklama ,
								değer : bu . ayarlar . yerler [ anahtar ]
							} ) )
						} ) ) ;
						
						ayarlarÖğeler . itin ( BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SettingsPanelList ,  {
							başlık : "Görüntüleme Rozetleri:" ,
							çocuklar : Nesne . tuşları ( bu . varsayılan . rozetler ) . map ( key  =>  BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SettingsSaveItem ,  {
								tip : "Anahtar" ,
								eklenti : bu ,
								anahtarlar : [ "rozetler" ,  anahtar ] ,
								etiket : anahtar . böl ( "_" ) . map ( n  =>  BDFDB . LibraryModules . StringUtils . upperCaseFirstChar ( n . toLowerCase ( ) ) ) . katıl ( " " ) ,
								değer : bu . ayarlar . rozetler [ anahtar ] ,
								etiketÇocuklar : bu . createAyarlarBadges ( anahtar )
							} ) ) . concat ( Object . tuşları ( bu . varsayılan . göstergeleri ) . map ( key  =>  BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SettingsSaveItem ,  {
								tip : "Anahtar" ,
								eklenti : bu ,
								tuşlar : [ "göstergeler" ,  tuş ] ,
								etiket : anahtar . böl ( "_" ) . map ( n  =>  BDFDB . LibraryModules . StringUtils . upperCaseFirstChar ( n . toLowerCase ( ) ) ) . katıl ( " " ) ,
								değer : bu . ayarlar . göstergeler [ anahtar ] ,
								etiketÇocuklar : bu . createAyarlarBadges ( anahtar )
							} ) ) ))
						} ) ) ;
						
						ayarlarÖğeler . itin ( BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SettingsItem ,  {
							tip : "Düğme" ,
							renk : BDFDB . Kütüphane Bileşenleri . Düğme . Renkler . KIRMIZI ,
							etiket : "Önbelleğe alınmış Rozet Verilerini Sıfırla" ,
							onClick : _  =>  BDFDB . ModalUtils . onaylayın ( bu ,  "Rozet Önbelleğini sıfırlamak istediğinizden emin misiniz? Bu, tüm Rozetleri yeniden oluşturmaya zorlayacaktır." ,  _  =>  {
								BDFDB . DataUtils . kaldır ( bu ,  "rozet Önbelleği" ) ;
								bu . forceUpdateAll ( ) ;
							} ) ,
							çocuklar : BDFDB . DilUtils . Dil Dizeleri . SIFIRLA
						} ) ) ;
						
						dönüş  ayarlarıÖğeler ;
					}
				} ) ;
			}

			onSettingsClosed  ( )  {
				if  ( bu . SettingsUpdated )  {
					silmek  bu . AyarlarGüncellendi ;
					bu . forceUpdateAll ( ) ;
				}
			}
			
			forceUpdateAll  ( )  {				
				BDFDB . PatchUtils . forceAllUpdates ( bu ) ;
				BDFDB . MessageUtils . rerenderAll ( ) ;
			}

			processMemberListItem  ( e )  {
				if  ( ! e . örnek . sahne . kullanıcı  ||  ! bu . ayarlar . yerler . üyeList )  döner ;
				bu . injectBadges ( BDFDB . ObjectUtils . get ( e . returnvalue ,  "props.decorators.props.children" ) ,  e . instance . props . user ,  e . instance . props . channel . guild_id ,  "list" ) ;
			}

			processMessageUsername  ( e )  {
				if  ( ! e . örnek . sahne . ileti  ||  ! bu . ayarlar . yerler . sohbet )  geri dönerse ;
				const  yazar  =  e . örnek . sahne . kullanıcıOverride  ||  e . örnek . sahne . mesaj . yazar ;
				bu . injectBadges ( e . returnValue . sahne . Çocukların ,  yazar ,  ( BDFDB . LibraryModules . ChannelStore . getChannel ( e . örneğinin . sahne . mesajı . channel_id )  ||  { } ) . guild_id ,  "sohbet" ) ;
			}
			
			processUserProfileBadgeList  ( e )  {
				if  ( e . örnek . props . özel )  {
					için  ( izin  i  içinde  e . returnValue . sahne . Çocuk )  halinde  ( E . returnValue . sahne . çocuklar [ i ] )  {
						izin  anahtar  =  parseInt ( e . returnValue . sahne . çocuklar [ I ] . anahtar ) ;
						let  keyName  =  e . örnek . sahne . filtre  &&  Nesne . tuşları ( bu . varsayılan . rozetler ) . find ( n  =>  bu . varsayılanlar . rozetler [ n ] . anahtarlar . içerir ( anahtar ) ) ;
						if  ( keyName  &&  ! bu . ayarlar . rozetler [ keyName ] )  e . returnValue . sahne . çocuklar [ ben ]  =  boş ;
						else  if  ( e . dönüş değeri . props . çocuklar [ i ] . type . displayName  ==  "TooltipContainer"  ||  e . returnvalue . props . çocuklar [ ben ] . type . displayName  ==  " Araç ipucu" )  {
							const  çocuklarRender  =  e . returnValue . sahne . çocuklar [ ben ] . sahne . çocuklar ;
							e . returnValue . sahne . çocuklar [ ben ] . sahne . çocuklar  =  ( ... args )  =>  {
								const  çocuklar  =  çocuklarRender ( ... args ) ;
								 çocukları silin . sahne . onClick ;
								 çocukları iade et ;
							} ;
							e . returnValue . sahne . çocuklar [ ben ]  =  BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . TooltipContainer ,  e . returnValue . sahne . çocuklar [ I ] . sahne ) ;
						}
					}
					if  ( ( bu . ayarlar . göstergeler . CURRENT_GUILD_BOOST  ||  ! e . instance . props . filter )  &&  e . instance . props . premiumCurrentGuildSince )  e . returnValue . sahne . çocuklar . itme ( BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents .Araç İpucuKonteyner ,  {
						metin : BDFDB . DilUtils . LanguageStringsFormat ( "PREMIUM_GUILD_SUBSCRIPTION_TOOLTIP" ,  e . instance . props . premiumCurrentGuildSince ) ,
						çocuklar : BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . Clickable ,  {
							className : BDFDB . diskCN . kullanıcı rozetiouter ,
							çocuklar : BDFDB . ReactUtils . createElement ( "div" ,  {
								className : BDFDB . diskCNS . kullanıcı rozeti  +  BDFDB . diskCN . _badgeseverywheregöstergesi ,
								çocuklar : BDFDB . ReactUtils . createElement ( BDFDB . LibraryComponents . SvgIcon ,  {
									className : BDFDB . diskCN . üyepremium simgesi ,
									isim : BDFDB . Kütüphane Bileşenleri . SvgIcon . İsimler . YÜKSELTME
								} )
							} )
						} )
					} ) ) ;
					eğer  ( ! e . returnValue . sahne . Çocukların . filtresi ( n  =>  n ) . uzunluk )  dönüş  boş ;
				}
			}

			injectBadges  ( çocuklar ,  kullanıcı ,  guildId ,  tür )  {
				eğer  ( ! BDFDB . ArrayUtils . olan ( çocuk )  ||  ! kullanıcı  ||  kullanıcı . isNonUserBot ( ) )  return ;
				eğer  ( ! loadedUsers [ kullanıcı . id ]  ||  ( ( yeni  tarihi ( ) ) . getTime ( )  -  loadedUsers [ kullanıcı . id ] . tarih  > =  1000 * 60 * 60 * 24 * 7 ) )  {
					sıralıÖrnekler [ kullanıcı . kimlik ]  =  [ ] . concat ( querydInstances [ kullanıcı . id ] ) . filtre ( n  =>  n ) ;
					eğer  ( requestQueue . kuyruğu . indexOf ( kullanıcı . id )  ==  - 1 )  requestQueue . sıra . itme ( Kullanıcı . id ) ;
					bu . runQueue ( ) ;
				}
				çocuklar . push ( BDFDB . ReactUtils . createElement ( sınıf  BDFDB'yi genişletir  . ReactUtils . Bileşen { 
					render ( )  {
						eğer  ( ! loadedUsers [ kullanıcı . id ]  ||  ( ( yeni  tarihi ( ) ) . getTime ( )  -  loadedUsers [ kullanıcı . id ] . tarih  > =  1000 * 60 * 60 * 24 * 7 ) )  {
							if  ( querydInstances [ kullanıcı . id ] . indexOf ( this )  ==  - 1 )  QueuedInstances [ kullanıcı . kimlik ] . itin ( bu ) ;
							 boş dön ;
						}
						Başka  dönüş  _this . createBadges ( kullanıcı ,  guildId ,  type ) ;
					}
				} ,  { } ,  doğru ) ) ;
			}
			
			runQueue  ( )  {
				if  ( ! requestQueue . id )  {
					izin ver  id  =  requestQueue . sıra . kaydırma ( ) ;
					eğer  ( kimlik )  {
						requestQueue . kimlik  =  kimlik ;
						BDFDB . TimeUtils . temizle ( requestQueue . zaman aşımı ) ;
						requestQueue . zaman aşımı  =  BDFDB . TimeUtils . zaman aşımı ( _  =>  {
							requestQueue . kimlik  =  boş ;
							bu . runQueue ( ) ;
						} ,  30000 ) ;
						BDFDB . Kütüphane Modülleri . UserProfileUtils . fetchProfile ( id ) ;
					}
				}
			}

			createBadges  ( kullanıcı ,  guildId ,  tür )  {
				 fakeGuildBoostDate'e izin verin ;
				if  ( typeof  user . id  ==  "string"  &&  user . id . startWith ( specialFlag  +  "GB" ) )  {
					izin  düzeyi  =  parseInt ( Kullanıcı . id . bölme ( "_" ) . pop ( ) ) ;
					for  ( let  i  =  0 ;  i  <  100  &&  ! fakeGuildBoostDate ;  i ++ )  {
						let  tarih  =  yeni  Tarih ( )  -  1000 * 60 * 60 * 24 * 15  *  i ;
						Eğer  ( seviyesindeki  ==  BDFDB . LibraryModules . GuildBoostUtils . getUserLevel ( tarih ) )  fakeGuildBoostDate  =  tarihi ;
					}
				}
				let  üye  =  guildId  &&  BDFDB . Kütüphane Modülleri . Üye Mağazası . GetMember ( guildId ,  kullanıcı . id ) ;
				 BDFDB'yi iade edin . ReactUtils . createElement ( BDFDB . LibraryComponents . UserBadges . varsayılan ,  {
					className : BDFDB . DOMUtils . formatClassName ( BDFDB . disCN . _badgeseverywherebadges ,  BDFDB . disCN [ `_badgeseverywherebadges $ { türü } ' ] ) ,
					kullanıcı : kullanıcı ,
					boyut : BDFDB . Kütüphane Bileşenleri . Kullanıcı Rozetleri . RozetBoyutları . SIZE_18 ,
					gelenek : doğru ,
					filtre : yazın  !=  "ayarlar" ,
					premiumSince : yüklenenKullanıcılar [ kullanıcı . id ]  &&  yüklenenKullanıcılar [ kullanıcı . kimlik ] . premium_since ? Yeni  Tarih ( loadedUsers [ kullanıcı . id ] . premium_since ) : ( kullanıcı . id  ==  ( specialFlag  +  "NİTRO" ) ? yeni  tarihi ( ) : boş ) ,
					premiumGuildSince : fakeGuildBoostDate  ||  ( LoadedUsers [ kullanıcı . İd ]  &&  loadedUsers [ kullanıcı . İd ] . Premium_guild_since ? Yeni  tarihi ( loadedUsers [ kullanıcı . İd ] . Premium_guild_since ) : boş ) ,
					premiumCurrentGuildSince : üye  &&  üye . premiumSince  &&  yeni  Tarih ( üye . premiumSince )  ||  kullanıcı . id  ==  ( specialFlag  +  "CGB" )  &&  yeni  Tarih ( )
				} ) ;
			}
			
			createSettingsBadges  ( bayrak )  {
				let  sarmalayıcılar  =  [ ] ;
				if  ( bu . varsayılanlar . göstergeler [ bayrak ] )  {
					let  id  =  flag  ==  "CURRENT_GUILD_BOOST" ? ( specialFlag  +  "CGB" ) : boş ;
					 kullanıcıya  izin ver =  yeni  BDFDB . DiscordObjects . Kullanıcı ( { flags : 0 ,  id : id } ) ;
					sarmalayıcılar . push ( bu . createBadges ( user ,  null ,  "ayarlar" ) ) ;
				}
				Başka  için  ( izin  anahtar  arasında  bu . varsayılan . rozetler [ işaretle ] . şifreler )  {
					let  userFlag  =  flag  ==  "PREMIUM"  ||  flag  ==  "PREMIUM_GUILD_SUBSCRIPTION" ? 0 : BDFDB . Uyuşmazlık Sabitleri . UserFlags [ bayrak ] ;
					let  keyName  =  BDFDB . Kütüphane Bileşenleri . UserBadgesKeys [ anahtar ] ;
					if  ( userFlag  ==  null  &&  keyName )  userFlag  =  BDFDB . Uyuşmazlık Sabitleri . UserFlags [ keyName ]  !=  null ? BDFDB . Uyuşmazlık Sabitleri . Kullanıcı Bayrakları [ anahtarAdı ] : BDFDB . Uyuşmazlık Sabitleri . Kullanıcı Bayrakları [ Nesne . anahtarlar ( BDFDB . DiscordConstants . UserFlags ) . bul (f  =>  f . indexOf ( keyName )  >  - 1  ||  anahtarAdı . indexOf ( f )  >  - 1 ) ] ;
					if  ( userFlag  !=  null )  {
						let  id ;
						if  ( flag  ==  "PREMIUM" )  id  =  özelFlag  +  "NITRO" ;
						Başka  eğer  ( keyName  &&  keyName . startswith ( "PREMIUM_GUILD_SUBSCRIPTION" ) )  id  =  specialFlag  +  "GB_"  +  keyName . böl ( "_" ) . pop ( ) ;
						 kullanıcıya  izin ver =  yeni  BDFDB . DiscordObjects . Kullanıcı ( { flags : userFlag ,  id : id } ) ;
						sarmalayıcılar . push ( bu . createBadges ( user ,  null ,  "ayarlar" ) ) ;
					}
				}
				geri  sarmalayıcılar ;
			}
		} ;
	} ) ( pencere . BDFDB_Global . PluginUtils . buildPlugin ( config ) ) ;
} ) ( ) ;
