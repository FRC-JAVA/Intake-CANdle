# Intake-CANdle

```java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot.subsystems.leds;

import com.ctre.phoenix.led.CANdle;
import com.ctre.phoenix.led.CANdleConfiguration;
import com.ctre.phoenix.led.StrobeAnimation;
import com.ctre.phoenix.led.TwinkleAnimation;
import com.ctre.phoenix.led.CANdle.LEDStripType;
import com.ctre.phoenix.led.TwinkleAnimation.TwinklePercent;

import edu.wpi.first.wpilibj.DriverStation;
import edu.wpi.first.wpilibj.DriverStation.Alliance;
import edu.wpi.first.wpilibj2.command.SubsystemBase;
import frc.robot.subsystems.limelight.LimelightHelpers;
import java.util.Optional;

public class Leds extends SubsystemBase {

  Optional<Alliance> alliance;
  private TwinkleAnimation twinkleblueAnimation;
  private TwinkleAnimation twinkleredAnimation;
  private StrobeAnimation strobeAnimation;
  private TwinkleAnimation twinkleblueguitarAnimation;
  private TwinkleAnimation twinkleredguitarAnimation;
  //private TwinkleAnimation twinkleintakeAnimation;
  private StrobeAnimation strobeintakeAnimation;

  private final int THUNDERSTUCK_LEDS = 68;
  private final int GUITAR_LEDS = 80;
  private final int TOTAL_LEDS = 148;
  private final int GUITAR_LEDS_OFFSET = 68;
  private final int THUNDERSTUCK_LEDS_OFFSET = 0;
  private final int TOTAL_LED_OFFSET = 0;
  private final double STROBE_SPEED = 98.0 / 256.0;
  private final int LedCount = 148;


  private CANdle candle1;
  private static boolean intaking;

  /** Creates a new CANdle */
  public Leds() {
    setupLeds();
    CANdleConfiguration configAll = new CANdleConfiguration();
    configAll.statusLedOffWhenActive = true;
    configAll.stripType = LEDStripType.GRB;
    candle1.configAllSettings(configAll, 100);
  }

  private void setupLeds() {
    // twinkleblueAnimation = new TwinkleAnimation(0, 0, 255);
    twinkleblueAnimation = new TwinkleAnimation(0,0,255,0,0,LedCount,TwinklePercent.Percent30,TOTAL_LED_OFFSET);
    // twinkleredAnimation = new TwinkleAnimation(255, 0, 0);
    twinkleredAnimation = new TwinkleAnimation(255,0,0,0, 0,TOTAL_LEDS,TwinklePercent.Percent76,0);
    candle1 = new CANdle(48, "canivore");
    // strobeAnimation = new StrobeAnimation(0, 255, 0, 0, 98.0 / 256.0, 68);
    strobeAnimation = new StrobeAnimation(0, 255, 0, 255, STROBE_SPEED, THUNDERSTUCK_LEDS, 0);
    twinkleblueguitarAnimation = new TwinkleAnimation(0,0,255,0,0,TOTAL_LEDS,TwinklePercent.Percent88,GUITAR_LEDS_OFFSET);
    twinkleredguitarAnimation = new TwinkleAnimation( 255,0, 0, 0, 0,TOTAL_LEDS,TwinklePercent.Percent30,GUITAR_LEDS_OFFSET);

    ///twinkleintakeAnimation = new TwinkleAnimation(0, 0, 0, 255, STROBE_SPEED, TOTAL_LEDS, null, 0);
    strobeintakeAnimation = new StrobeAnimation(0, 0, 0, 255, STROBE_SPEED, TOTAL_LEDS, TOTAL_LED_OFFSET);

    intaking = false;

    setLedsUsingAllianceColor();
    setLedsIntake();
  }

  @Override
  public void periodic() {
    if ((LimelightHelpers.getTX("limelight") < 3) &&
        (LimelightHelpers.getTY("limelight") < 3) &&
        (LimelightHelpers.getTX("limelight") > -3) &&
        (LimelightHelpers.getTY("limelight") > -3) &&
        LimelightHelpers.getTV("limelight")) {
      setLedsToStrobe();
        }
    else if(intaking){
      //setLedsToStrobe();
      setLedsIntake();
    }
    else {
      //candle1.animate(strobeintakeAnimation);
      setLedsUsingAllianceColor();
    }
    // This method will be called once per scheduler run
  }

  public void setLedsToStrobe() {
    candle1.animate(strobeAnimation);
    alliance = DriverStation.getAlliance();
    if (alliance.isPresent()) {
      if (alliance.get() == Alliance.Red) {
        candle1.animate(twinkleredguitarAnimation);
      }
      if (alliance.get() == Alliance.Blue) {
        // Put what you want it to do here.
        candle1.animate(twinkleblueguitarAnimation);
      }
    }
  }

  public void setLedsIntake() {
    // Put what you want them to do in here.
    candle1.animate(strobeintakeAnimation);
  }

  public void setLedsUsingAllianceColor() {
    alliance = DriverStation.getAlliance();
    if (alliance.isPresent()) {
      if (alliance.get() == Alliance.Red) {
        candle1.animate(twinkleredAnimation);
      }
      if (alliance.get() == Alliance.Blue) {
        // Put what you want it to do here.
        candle1.animate(twinkleblueAnimation);
      }
    }
  }

  

  public static void setIntakeStatus(boolean status) {
    intaking = status;
  }
}
```
