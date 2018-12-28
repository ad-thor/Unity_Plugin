
  # Getting Started with the Appo Unity Plugin

  * [Before You Start](#start)
  * [SDK Set Up](#step1)
    - [Rewarded Video](#rewardedvideo)
    - [Interstitial](#interstitial)
  * [Additional Settings for iOS](#step2)
  * [Additional Settings for Android](#step3)
  * [Appo SDK API Reference](#ApiReference)
  * [Sample Code](#sample_code)

  ## <a name="start">Before You Start</a>


  * Support rewarded video only;
  * Support Unity4.x, Unity5.x, Unity2017;
  * Support iOS 8.0+;
  * Support Android API Level 15+;
  * [Click Here to Download Latest SDK;](https://github.com/ad-thor/Unity_Plugin/blob/master/U3D-AppoSDK.unitypackage.zip)

  ## <a name="step1">Step 1. Appo Unity SDK Set Up</a>

  * Import U3D-AppoSDK.unitypackage to your U3D project.
  
  * Create a script and attach it to [Main Camera]. Then implement the Awake function as belows.

  ```
  using AppoServiceSDK;
  void Awake () {
  	AppoService.initSDK (slot_id);
  	AppoService.uploadConsent ("yes", "GDPR");
  }
  ```

  * Use this interface to upload consent from affected users.
    ```
    AppoService.uploadConsent (consentValue, consentType);
    ```

  ###  <a name="rewardedvideo">Adding the Rewarded Video Ad API in iOS</a>

  * [For Rewarded Video] Create a script and attach it to a Unity UIController which you'd like to show rewarded video on. Then implement the Start function as belows.

  ```
  void Start () {
  	//load rewardvideo ad
  	AppoService.loadRewardedVideo (slot_id);
  }

  void OnEnable() {
  	AppoService.rewardVideoLoadSuccess   += AppoRewardVideoLoadSuccess;
  	AppoService.rewardVideoLoadingFailed += AppoRewardVideoLoadingFailed;
  }

  void OnDisable(){
  	AppoService.rewardVideoLoadSuccess -= AppoRewardVideoLoadSuccess;
  	AppoService.rewardVideoLoadingFailed -= AppoRewardVideoLoadingFailed;
  }

  void OnDestroy(){
  	AppoService.release ();
  }

  void AppoRewardVideoLoadSuccess(){

  }

  void AppoRewardVideoLoadingFailed(string error){
  	Debug.Log ("U3D delegate, AppoRewardVideoLoadingFailed. " + error);
  }
  ```
  * [For Rewarded Video] Implement a button click event to check if we can show rewarded video ad.For the first time you request rewarded video ads, you may need to try serveral times.Because it takes some time to download video files.Once a video file downloads completely, it will response immediately.

  ```
  void playBtnClick(){
  	//show reward video if it's avalable
  	if(AppoService.isRewardedVideoReady ())
  		AppoService.showRewardedVideo (slot_id);
  	else
  		Debug.Log ("Appo Rewarded Video is not ready");
  }
  ```

  ### <a name="interstitial">Adding the Interstitial Ad API in iOS</a>

  * [For Interstitial] Create a script and attach it to a Unity UIController which you'd like to show intersitial ad on. Then implement the Start function as belows.

  ```
  void Start () {
  	//load interstitial ad
  	AppoService.preloadInterstitialAD (slot_id);
  }

  void OnEnable() {
  	AppoService.interstitialLoadSuccess   += AppoInterstitialLoadSuccess;
  	AppoService.interstitialLoadFailed += AppoInterstitialLoadingFailed;
  }

  void OnDisable(){
  	AppoService.interstitialLoadSuccess -= AppoInterstitialLoadSuccess;
  	AppoService.interstitialLoadFailed -= AppoInterstitialLoadFailed;
  }

  void OnDestroy(){
  	AppoService.release ();
  }

  void AppoInterstitialLoadSuccess(){

  }

  void AppoInterstitialLoadingFailed(string error){
  	Debug.Log ("U3D delegate, AppoInterstitialLoadFailed. " + error);
  }
  ```

  * [For Interstitial] Implement a button click event to check if we can show rewarded video ad.

  ```
  void showBtnClick(){
  	//show interstitial if it's avalable
  	if(AppoService.isInterstitialAvailable ())
  		AppoService.showInterstitial ();
  	else
  		Debug.Log ("Appo Interstitial is not ready");
  }
  ```

  * For more delegate details, you should check the [**API Reference**](#ApiReference).


  ## <a name="step2">Additional Settings for iOS</a>

  *  For Unity4.x you need to do some configuration or using other method to do that(eg: XUPorter):
  	- 1.Build Xcode project.
  	- 2.copy ApplinsSDK.Framework and AppoServiceCWrapper.mm to your Xcode project manually.
  	- 3.Add a static link to: Build Settings -> Other Linker Flags -> -ObjC
  	- 4.In Info.plist added the NSAppTransportSecurity, the type for Dictionary. In NSAppTransportSecurity added the NSAllowsArbitraryLoads the Boolean,setting YES.
  	- 5.Import libz.tbd in Project -> Target -> Build Phases -> Link Binary With Libraries.
  	- 6.Build Settings -> Deplyment Info -> Deployment target -> 8.0

  *  For Unity 5(include 2017 and 2018), you could import OnPostProcessBuild.cs to Editor directory. It will config plist and xcode building property for you (eg: add a static link to Other Linker Flags with '-ObjC' and add NSAppTransportSecurity in the plist). 
  
  *  For Unity Cloud Build, you must import OnPostProcessBuild.cs to Editor directory. Then Collab with Unity Cloud and build.

  ## <a name="step3">Additional Settings for Android</a>

  * Open 'File' -> 'Build Settings' -> 'Player Settings', and make sure 'Minimum API Level' later than 'API Level 15'. We recommend using the latest version of Android SDK and Build Tools.
  * Select SDK folder Plugin->Android->AppoSDK in Project. Check the Android box in the inspector.

  ## <a name="ApiReference">SDK API reference</a>

  ```
  /**
   Get Appo AD Config in Appdelegate(didFinishLaunchingWithOptions:)
   @param slot_id Ad
  */
  public static void initSDK(string slot_id)

  /**
  Use this interface to upload consent from affected users 
  @param consentValue: "yes"/"no"
  @param consentType: "GDPR" or something else
  */
  public static void uploadConsent(string consentValue, string consentType)

  /**
  *  Get RewardVideo Ad
  *  Call (loadRewardedVideo) method get RewardVideo Ad！

    @param slot_id         AppoService AD ID
    */
    public static void loadRewardedVideo(string slot_id)


  /**
  *  show RewardVideo
  *  you should call it after rewardVideoLoadSuccess delegate function is invoked.
  */
    public static void showRewardedVideo()		

  /**
  * Check if RewardVideo is read 
  * if true, you can call showRewardVideo;
  */
    public static bool checkRewardVideoIsReady()

  /**
  Rewarded video ad delegate
  */

  /**
  *  video loaded successfully delegate. You shoud not call 'showRewardVideo' here, for android sdk would preload rewardedvideos, it may call this function serveral times.
  **/
    public static event Action rewardVideoLoadSuccess;
    
  /**
  *  video loaded failed delegate
  **/
    public static event Action<string> rewardVideoLoadingFailed;

  /**
  *  begin playing Ad delegate
  **/
    public static event Action rewardVideoDidStartPlaying;

  /**
  *   playing Ad finished delegate
  **/
    public static event Action rewardVideoDidFinishPlaying;

  /**
  *   user clicking Ads delegate only for iOS
  **/
    public static event Action rewardVideoDidClickRewardAd;

  /**
  *  will leave Application delegate only for iOS
  **/
    public static event Action rewardVideoWillLeaveApplication;


  /**
  *  jumping AppStroe failure delegate only for iOS
  **/
    public static event Action rewardVideoJumpfailed;


  /**
  *  reward the player here
  **/
    public static event Action<string> rewardVideoAdRewarded;

  /**
  *  reward video ad closed delegate
  **/
    public static event Action rewardVideoClosed;


  /**
   Get Interstitial Ad
   First,you should Call (loadInterstitialWithSlotId) method get Interstitial！
   Then On his success delegate method invokes (showInterstitia） method
  @param slot_id         AppoService AD ID
   */
  public static void preloadInterstitialAD(string slot_id)
  		
  /**
  show showInterstitial	you should call it in the loadInterstitialWithSlotId delegate function.
  */
  public static void showInterstitial()
  		
  /**
  Check if Interstitial is read 
  if true, you can call showInterstitial;
  */
  public static bool isInterstitialAvailable()
  		
  /**
  *  Interstitial is loaded successfully, you can call showInterstitial() in this function.
  **/
    public static event Action interstitialLoadSuccess;

  /**
  *  Interstitial is loaded failed;
  **/
    public static event Action<string> interstitialLoadFailed;

  /**
  *   user click Ads
  **/
    public static event Action interstitialDidClickRewardAd;

  /**
  *  jump AppStroe failed
  **/
    public static event Action interstitialJumpfailed;

  /**
  *  Interstitial is hidden
  **/
    public static event Action interstitialClose;	
  ```

  ##  <a name="sample_code"> Sample Code </a>

   **AppoCamera.cs** attaches to [Main Camera]

  ```
  using System;
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;
  using AppoServiceSDK;

  public class AppoCamera : MonoBehaviour {
  	#if UNITY_ANDROID
  	private string slot_id = "72666429";
  	#elif UNITY_IOS
  	private string slot_id = "30769964";
  	#endif

  	void Awake () {
  		AppoService.initSDK (slot_id);
  		AppoService.uploadConsent ("yes", "GDPR");
  	}
  }
  ```

  **AppoRewardedVideo.cs** attaches to a Unity GameObject which you'd like to show rewarded video.

  ```
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;
  using UnityEngine.UI;
  using AppoServiceSDK;

  public class AppoRewardedVideo : MonoBehaviour {
  	#if UNITY_ANDROID
  	private string slot_id = "72666429";
  	#elif UNITY_IOS
  	private string slot_id = "30769964";
  	#endif
  	//notice: attach your UI objcet here
  	public Button loadBtn;
  	public Button playBtn;
  	public Text statusText;

  	void Start () {
  		playBtn.onClick.AddListener (playBtnClick);
  		loadBtn.onClick.AddListener (loadBtnClick);
  		//Notice: load rewardvideo ad when you init UI.
  		AppoService.loadRewardedVideo(slot_id); 
  	}

  	//set delegate
  	void OnEnable() {
  		AppoService.rewardVideoLoadSuccess += AppoRewardVideoLoadSuccess;
  		AppoService.rewardVideoLoadingFailed += AppoRewardVideoLoadingFailed;
  		AppoService.rewardVideoDidStartPlaying += AppoRewardVideoDidStartPlaying;
  		AppoService.rewardVideoDidFinishPlaying += AppoRewardVideoDidFinishPlaying;
  		AppoService.rewardVideoDidClickRewardAd += AppoRewardVideoDidClickRewardAd;
  		AppoService.rewardVideoWillLeaveApplication += AppoRewardVideoWillLeaveApplication;
  		AppoService.rewardVideoJumpfailed += AppoRewardVideoJumpfailed;
  		AppoService.rewardVideoAdRewarded += AppoRewardVideoAdRewarded;
  		AppoService.rewardVideoClosed += AppoRewardVideoClosed;
  	}

  	void OnDisable(){
  		AppoService.rewardVideoLoadSuccess -= AppoRewardVideoLoadSuccess;
  		AppoService.rewardVideoLoadingFailed -= AppoRewardVideoLoadingFailed;
  		AppoService.rewardVideoDidStartPlaying -= AppoRewardVideoDidStartPlaying;
  		AppoService.rewardVideoDidFinishPlaying -= AppoRewardVideoDidFinishPlaying;
  		AppoService.rewardVideoDidClickRewardAd -= AppoRewardVideoDidClickRewardAd;
  		AppoService.rewardVideoWillLeaveApplication -= AppoRewardVideoWillLeaveApplication;
  		AppoService.rewardVideoJumpfailed -= AppoRewardVideoJumpfailed;
  		AppoService.rewardVideoAdRewarded -= AppoRewardVideoAdRewarded;
  		AppoService.rewardVideoClosed -= AppoRewardVideoClosed;
  	}

  	void OnDestroy(){
  		//do not forget to call release, otherwise android platform will casue memory leak.
  		AppoService.release ();
  	}

  	//Notice: You should call this api as soon as you can. For example, call it in Start function.(not in awake, beacause we must call AppoService.initSDK first in camera awake function)
  	//For convenience test, we add a button to click.
  	void loadBtnClick(){
  		//load rewardvideo ad
  		AppoService.loadRewardedVideo (slot_id);
  	}

  	void playBtnClick(){
  		//you can also use this api to check if rewearded video is ready.
  		if (AppoService.isRewardedVideoReady ()) {
  			setReady (true, null);
  			AppoService.showRewardedVideo (slot_id);
  		}
  		else
  			Debug.Log ("Appo Rewarded Video is not ready");
  	}

  	void setReady(bool isReady, string msg){
  		if (isReady) {
  			statusText.color = Color.green; 
  			statusText.text = "isReadyToPlay: Yes";
  		} else {
  			statusText.color = Color.red; 
  			statusText.text = msg;
  		}
  	}
  		
  	/**
  	 * 
     * reward video delegate
     * 
     * 
     */

  	//video load success. 
  	//Do not show reward video in the function, for android sdk preloads ads, may call this function several times.
  	void AppoRewardVideoLoadSuccess(){
  		Debug.Log ("U3D delegate, AppoRewardVideoLoadSuccess");
  		setReady (true, null);
  	}

  	//video load failure
  	void AppoRewardVideoLoadingFailed(string error){
  		setReady (false, error);
  		Debug.Log ("U3D delegate, AppoRewardVideoLoadingFailed. " + error);
  	}
  		
  	//start playing video
  	void AppoRewardVideoDidStartPlaying(){
  		Debug.Log ("U3D delegate, AppoRewardVideoDidStartPlaying");
  	}

  	//finish playing video
  	void AppoRewardVideoDidFinishPlaying(){
  		Debug.Log ("U3D delegate, AppoRewardVideoDidFinishPlaying");
  	}

  	//click ad
  	void AppoRewardVideoDidClickRewardAd(){
  		Debug.Log ("U3D delegate, AppoRewardVideoDidClickRewardAd");
  	}
  		
  	//will leave Application, only for iOS
  	void AppoRewardVideoWillLeaveApplication(){
  		Debug.Log ("U3D delegate, AppoRewardVideoWillLeaveApplication");
  	}
  		
  	//jump to AppStroe failed, only for iOS
  	void AppoRewardVideoJumpfailed(){
  		Debug.Log ("U3D delegate, AppoRewardVideoWillLeaveApplication");
  	}

  	//players get rewarded here
  	void AppoRewardVideoAdRewarded(string rewardVideoNameAndAmount){
  		Debug.Log ("U3D delegate, AppoRewardVideoAdRewarded, " + rewardVideoNameAndAmount);
  	}

  	//close video ad
  	void AppoRewardVideoClosed(){
  		Debug.Log ("U3D delegate, AppoRewardVideoClosed");
  		setReady (false, "video play end");
  	}
  }

  ```
  **AppoInterstitial.cs** attaches to a Unity GameObject which you'd like to show interstitail.

  ```
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;
  using UnityEngine.UI;
  using AppoServiceSDK;

  public class AppoInterstitial : MonoBehaviour {
  	#if UNITY_ANDROID
  	private string slot_id = "11546588";
  	#elif UNITY_IOS
  	private string slot_id = "53479848";
  	#endif
  	//notice: attach your UI objcet here
  	public Button loadBtn;
  	public Button showBtn;
  	public Text statusText;

  	// Use this for initialization
  	void Start () {
  		showBtn.onClick.AddListener (showBtnClick);
  		loadBtn.onClick.AddListener (loadBtnClick);
  		//Notice: load Interstitial ad when you init UI.
  		AppoService.preloadInterstitialAD (slot_id); 
  	}
  	
  	//set delegate
  	void OnEnable() {
  		setupDelegates();
  	}

  	//set delegate
  	void setupDelegates(){
  		AppoService.interstitialLoadSuccess += AppoInterstitialLoadSuccess;
  		AppoService.interstitialLoadFailed += AppoInterstitialLoadingFailed;
  		AppoService.interstitialDidClickRewardAd += AppoInterstitialDidClickRewardAd;
  		AppoService.interstitialClose += AppoInterstitialClose;
  	}

  	void OnDisable(){
  		AppoService.interstitialLoadSuccess -= AppoInterstitialLoadSuccess;
  		AppoService.interstitialLoadFailed -= AppoInterstitialLoadingFailed;
  		AppoService.interstitialDidClickRewardAd -= AppoInterstitialDidClickRewardAd;
  		AppoService.interstitialClose -= AppoInterstitialClose;
  	}

  	void OnDestroy(){
  		//do not forget to call release, otherwise android platform will casue memory leak.
  		AppoService.release ();
  	}

  	void setReady(bool isReady, string msg){
  		if (isReady) {
  			statusText.color = Color.green; 
  			statusText.text = "isReadyToShow: Yes";
  		} else {
  			statusText.color = Color.red; 
  			statusText.text = msg;
  		}
  	}

  	void loadBtnClick(){
  		//load Interstitial ad
  		AppoService.preloadInterstitialAD (slot_id);
  		Debug.Log ("Appo Interstitial loadBtnClick");
  	}

  	void showBtnClick(){
  		//you can also use this api to check if Interstitial is ready.
  		if (AppoService.isInterstitialAvailable ()) {
  			setReady (true, null);
  			AppoService.showInterstitial ();
  		}
  		else
  			Debug.Log ("Appo Interstitial is not ready");
  	}


  /**
   * 
   * Interstitial delegate
   * 
   * 
   */

    void AppoInterstitialLoadSuccess(){
        Debug.Log ("U3D delegate, AppoInterstitialLoadSuccess");
        setReady (true, null);
    }

  	void AppoInterstitialLoadingFailed(string error){
  		setReady (false, error);
  		Debug.Log ("U3D delegate, AppoInterstitialLoadingFailed. " + error);
  	}

  	//click ad, only for iOS
  	void AppoInterstitialDidClickRewardAd(){
  		Debug.Log ("U3D delegate, AppoInterstitialDidClickAd");
  	}

  	void AppoInterstitialClose(){
  		Debug.Log ("U3D delegate, AppoInterstitialClose");
  		setReady (false, @"isReadyToShow: NO");
  	}
  }

  ```
  **AppoOnPostProcessBuild.cs** added to assets/editor directory.
  ```
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;
  using System.IO;

  #if UNITY_EDITOR
  using UnityEditor;
  using UnityEditor.Callbacks;
  using UnityEditor.iOS.Xcode;
  using System.Xml;
  #endif

  public class AppoOnPostProcessBuild : Editor {
  	#if UNITY_IOS || UNITY_EDITOR  

  	[PostProcessBuild (100)]
  	public static void OnPostprocessBuild(BuildTarget BuildTarget, string path)  
  	{  
  		if (BuildTarget == BuildTarget.iOS)  
  		{  
  			string projPath = PBXProject.GetPBXProjectPath(path);  
  			PBXProject proj = new PBXProject();  
  			proj.ReadFromString(File.ReadAllText(projPath));  
  			 
  			//add Other link flag
  			string target = proj.TargetGuidByName(PBXProject.GetUnityTargetName());  
  			proj.AddBuildProperty (target, "OTHER_LDFLAGS", "-ObjC");
  			File.WriteAllText(projPath, proj.WriteToString()); 

  			//add framework
  			proj.AddFrameworkToProject (target, "AdSupport.framework", false);  
  			proj.AddFrameworkToProject (target, "StoreKit.framework", false);  
  			proj.AddFrameworkToProject (target, "AVFoundation.framework", false);  
  			proj.AddFrameworkToProject (target, "SystemConfiguration.framework", false);  
  			proj.AddFrameworkToProject (target, "JavaScriptCore.framework", false);  
  			proj.AddFrameworkToProject (target, "ImageIO.framework", false);  
  			proj.AddFrameworkToProject (target, "UIKit.framework", false);  
            proj.AddFrameworkToProject (target, "libz.1.tbd", false); 
  			File.WriteAllText(projPath, proj.WriteToString()); 

  			//add ATS in plist
  			string plistPath = path + "/Info.plist";  
  			PlistDocument plist = new PlistDocument();  
  			plist.ReadFromString(File.ReadAllText(plistPath));  
  			PlistElementDict dictTransportSecurity = plist.root ["NSAppTransportSecurity"].AsDict ();
  			dictTransportSecurity.SetBoolean("NSAllowsArbitraryLoads",true);
  			File.WriteAllText(plistPath, plist.WriteToString());
  		}  
  	}  

  	#endif
  }
  ```
