

## Getting Start

1. Create your game mode 

2. Create and set your player pawn and player controller like the First Person template

3. Open your player controller and set Player Camera Manager Class to "RSPlayerCameraManager"

4. Create a data asset inherited from RSRecoilDataAsset
   ![image-20240429173210625](https://raw.githubusercontent.com/VJien/img/master/imgimage-20240429173210625.png)

5. Set your recoil data at the beginning of the game, for example, at the BeginPlay of your player pawn

   1. if your first person mesh has default rotation(forward axis is not X) like the demo , you need create a scene component as the parent, then rotate the mesh
   2. Do not modify the root component's transform
      ![image-20240429173016213](https://raw.githubusercontent.com/VJien/img/master/imgimage-20240429173016213.png)

   ![image-20240429172506562](https://raw.githubusercontent.com/VJien/img/master/imgimage-20240429172506562.png)

6. When you shot, you need call the function "Fire"
   ![image-20240429173446337](https://raw.githubusercontent.com/VJien/img/master/imgimage-20240429173446337.png)

7. ﻿When you enter or quit the aiming state, you need to call the function  "SetAdsState", the effect is completely inconsistent when it is in the aiming state or not
   ![image-20240429173520430](https://raw.githubusercontent.com/VJien/img/master/imgimgimage-20240429173520430.png)

8. You can refer to other blueprint logic or animation blueprint logic in the demo case

9. Have fun!





## How  to set RSRecoilDataAsset

RecoilSystem used a real spring algorithm to simulate the recoil effect of gunfire. There are many parameters here, but some of them do not require too much attention. Below are some important parameters and usage instructions

Most parameters have comments, which can be seen by moving the mouse over them

There are a total of three effects, namely the Kick effect of the gun, the Kick effect of the perspective, and the Sway effect of the gun

- Enable GunKick/ Enable ViewKick /Enable Sway : Switch on and off three different effects separately, convenient for separate testing 
- GunKickConfig

  - YawDamping : High frequency vibration in the X(yaw) direction

    - Amplitude: The amplitude of the spring

    - Rigidity Ratio: Vibration frequency

    - Damping Ratio: The larger the value, the faster it returns to a stationary state

    - InitialPhase: The phase is usually reset to a random phase every time a shot is fired, which is usually set in Gunnick and Viewkick and will also be reset every time a shot is fired Generally, it can be zero at most time
  - YawDampingPeriodic: vibrate in a cyclic motion in the X direction ,like sin function
  - YawDampingLowFrequency: low frequency vibration in the X direction
  - PitchDamping:High frequency vibration in the Z direction
  - PitchDampingPeriodic: vibrate in a cyclic motion in the Z direction ,like sin function
  - GunKickTransition
    - RecoilMaxX/Y/Z: the max offset at the start of recoil
    - ResumeOffset: After recoil will into the resume calculation, this is the resume offset
    - ResumeSpeed: Resume speed, do not use too small value, it will be wrong result
    - ResumeMaxTime: Resume max time, also can not be a very small value
    - HipScale: Hip fire transition scale
- ViewKickConfig
  - PitchDampingPeriodic:Periodic pitch vibration，sin function
  - PitchDampingOffset:ViewKick Pitch vibration trend, with a frequency lower than PitchDamping1, responsible for upward trend
  - PitchDampingWeightConfig:Extend the scale of the PitchDampingOffset and PitchDampingPeriodic, if your do not give a valid curve, it will use the default effect；Usually, it is to make the lifting effect slower as it increases
    - PitchDampingScaleCurve_Periodic：The oscillation pitch is scaled based on the already generated pitch
    - PitchDampingScaleCurve_Offset： Offset (up) Pitch is scaled based on the already generated Pitch
    - StartFirePitchThreshold：Define a threshold before which a shot will have an additional special vibration scaling, otherwise only based on the vibration scaling according to the curve above

  - YawDampingPeriodic:Periodic yaw vibration，sin function
  - YawDampingOffset_Step_1：Yaw vibration trend, with a frequency lower than YawDamping1, responsible for left and right trend, will into step2 if pitch is greater than  YawDampingOffset_Step_1_PitchRange
  - YawDampingOffset_Step_1_PitchRange：Greater than this value will into step2, if pitch is less than this value, will only use YawDampingOffset_Step_1
  - YawDampingOffset_Step_2：Second step of YawDampingOffset, responsible for left and right trend, will not work if pitch is less than YawDampingOffset_Step_1_PitchRange or greater than YawDampingOffset_Step_2_PitchRange

- SwayConfig : Sway includes two calculations: spring and interp, and the following parameters will involve
  - Yaw/Pitch/AdsYaw/AdsPitch: The sway effect of guns in normal/Ads state
  - SwayInterpCenterOffset: Center of interp sway, weapon space
  - SwayDampingCenterOffset: Center of damping sway, weapon space
  - FallingSwayXXX: Some Sway effect parameters related to jump or falling

- WeaponRightHandGripSocket:This parameter needs to be set to the name of your right hand bone after equipping the weapon, as recoil will have the effect of pressing the gun with your right hand. RecoilSystem will  search  the position information of this bone point
