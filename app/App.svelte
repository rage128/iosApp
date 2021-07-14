<!-- svelte-ignore a11y-label-has-associated-control -->
<page actionBarHidden="true">
  <gridLayout>
      <flexboxLayout flexDirection="column" backgroundColor="#3c495e" >
         
          <flexboxLayout class="aHeader" backgroundColor="#4383b8">
              <label id="headText" text="Smt Share" on:loaded="{textLoaded}" />
              <stackLayout class="filler"></stackLayout>
              <image id="shareButton" src="font://&#xf064;" class="fas" on:tap="{ launchModal }"/>
          </flexboxLayout  >
  
          <stackLayout id="aContent" class="aContent" verticalAlignment="center" backgroundColor="#1c486b">
              <textView bind:this="{ msgBox }" class="textF" hint="Buraya Mesajınızı Yazın" />
          </stackLayout >
          <stackLayout height="200" backgroundColor="#1c486b"></stackLayout>
   
      </flexboxLayout>
      <image id="img" stretch="aspectFill" visibility="collapsed" />
  </gridLayout>

</page>

<script lang="typescript">
import { EventData, Label, StackLayout, View } from '@nativescript/core';
import { isAndroid, isIOS } from '@nativescript/core/platform';
import { fromData, ImageSource } from '@nativescript/core/image-source';
import * as SocialShare from "@nativescript/social-share";


    import { showModal } from 'svelte-native'
    import Yeni from './pages/Yeni.svelte'
    let msgBox;

    function launchModal(args: EventData){
        const view = args.object as View;
        const targetView = view.page.getViewById("aContent") as View;
        const screenShot = getImage(targetView);
        /*
        img.imageSource  = screenShot;
        img.visibility = 'visible';*/
        SocialShare.shareImage(screenShot,"deneme");
        //showModal({ page: Yeni, props: { msg: 'hi' } })
    }

    function getImage(view : View){
        if(view.ios){
            UIGraphicsBeginImageContextWithOptions(view.ios.frame.size, false, 0);
            view.ios.drawViewHieararchyInRectAfterScreenUpdates(
                CGRectMake(0,0, view.ios.frame.size.width, view.ios.frame.size.height), true);
            
            let imageFromCurrentImageContext = UIGraphicsGetImageFromCurrentImageContext();
            UIGraphicsEndImageContext();
            return fromData(UIImagePNGRepresentation(imageFromCurrentImageContext));

        }else if(view.android){
            view.android.setDrawingCacheEnabled(true);

            let bmp = android.graphics.Bitmap.createBitmap(view.android.getDrawingCache());

            view.android.setDrawingCacheEnabled(false);

            const source = new ImageSource();
            source.setNativeSource( bmp);
            return source;

        }

        return undefined;
    }

    function textLoaded(args : EventData){

        const lbl = args.object as Label;
        if(isAndroid){
            lbl.android.setGravity(17);

        }

    }
</script>

<style>
    
    #headText{
        color:#FFF;
        text-align: left!important;
        font-size: 25px;
    }
    #shareButton{
        -webkit-filter: drop-shadow(25px 25px 5px #000);
        filter: drop-shadow(25px 25px 5px #000);
        color:#FFF;
        height:80px;
     }
     .filler{
         flex-grow: 1;
     }
    .aHeader{
        height: 200px;
        padding-left:50px;
        padding-right:50px;
    }
    .aContent{
        flex:auto;
        text-align: center;
   
    }
     .textF{
        placeholder-color:#FFF;
        color:#FFF;
        font-size:30px;
    }

 
</style>
