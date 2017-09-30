// updated 12/2/2015
// changed pin locations and added LCD
 
/*
 *The circuit:
 * LCD RS pin to digital pin 2
 * LCD Enable pin to digital pin 3
 * LCD D4 pin to digital pin 4
 * LCD D5 pin to digital pin 5
 * LCD D6 pin to digital pin 6
 * LCD D7 pin to digital pin 7
 * LCD R/W pin to ground
 * LCD VSS pin to ground
 * LCD VCC pin to 5V
 * 10K resistor:
    * ends to +5V and ground
    * wiper to LCD VO pin (pin 3)
 * Reset/Start switch to digital pin 8
 * Target #1 to digital pin 9
 * Target #2 to digital pin 10
 * Target #3 to digital pin 11
 * Target #4 to digital pin 12
 * Target #5 to digital pin 13
 */

// constants won't change in program execution

const int targetPin[] = { 9 , 10 , 11 , 12 }; // relay to raise target connected to this pin
const int switchPin[] = { A1 , A2 , A3 , A4 }; // limit switch detecting hit ( 1 per target ) connected to this pin
const int resetSwitchPin = 8; // reset switch connected to this pin
const int number_of_targets = 4;
const float TTA = 60000; // Total Time Allowed for run in milliseconds ( minutes * 60 seconds/minute * 1000 milliseconds per second )
const float TAFS_Factor = 0.8; // TimeAllowedForShot Factor ( TAFS * TAFS_Factor = new TAFS )
const float TAFS_Minimum = 2.5 * 1000; // Minimum TimeAllowedForShot ( seconds * 1000 milliseconds )

// theses variables will change in program execution
float TAFS_initial = 15 * 1000; // TimeAllowedForShot in milliseconds
int hit = LOW;
int randomTarget = 0;
int previous_randomTarget_1 = 999; // set inital value high enough to not match initial selection
int previous_randomTarget_2 = 999; // set inital value high enough to not match initial selection
boolean continueProgram = false;
boolean resetSwitchVal = LOW;  // reset to LOW after switch is read HIGH & begins loop 
boolean Action_Value;

// these variable will be initialized in the program at the beginning of each loop
float elapsedTotalTime;
float elapsedTargetTime;
float beginningTotalTime; // beginning time in milliseconds
float beginningTargetTime; // beginning target time in milliseconds
float TAFS;
int NumberOfHits;
int NumberOfShots;

// include the library code ( c++ code ) :
//#include <LiquidCrystal.h>
// initialize the library with the numbers of the interface pins
//LiquidCrystal lcd( 2 , 3 , 4 , 5 , 6 , 7 );

// Begin setup() ==========
void setup()
{
    Serial.begin( 9600 );
    //Serial.println( TTA );
    //Serial.println( TAFS_Factor );
    //Serial.println( TAFS_Minimum );

    // initialize the pseudo random number generator by doing analogRead() on pin 0
    randomSeed( analogRead( A0 ) );

    //  initialize before doing pinMode in the hope of getting a better random seed???
    for ( int i = 0 ; i < number_of_targets ; i++ )
    {
        pinMode( targetPin[ i ] , OUTPUT );
        //digitalWrite( targetPin[ i ] , HIGH );
        pinMode( switchPin[ i ] , INPUT );
    }
    pinMode( resetSwitchPin , INPUT );
}
// End setup() ==========

// Begin Raise_Lower_All_Targets subroutine
void Raise_Lower_All_Targets( char Action[] )
{
    if ( Action == "Lower" )
    {
      Action_Value = HIGH;
      Serial.println( "Lower all targets" );
    }
    else
    {
      Action_Value = LOW;
      Serial.println( "Raise all targets" );
    }
    for ( int i = 0 ; i  < number_of_targets ; i++ )
    {
        digitalWrite( targetPin[ i ] , Action_Value );
    }  
}
// End Raise_Lower_All_Targets subroutine
  
// Begin loop() ==========
void loop()
{
    //Serial.println("Begin Program");
    ////Serial.print("continueProgram = ");
    ////Serial.println(continueProgram);
    //Serial.println("Press Activate Switch to Start");

    // do while ( continueProgram == false );
    do
    {
        // read reset switch and handle accordingly
        resetSwitchVal = digitalRead( resetSwitchPin );
        //Serial.print( "Reset Switch Value = " );
        //Serial.println( resetSwitchVal );
        if ( resetSwitchVal == HIGH )
        {
            // drop all targets...
            continueProgram = true;
            Raise_Lower_All_Targets( "Lower" );

            // clear the screen
            //lcd.clear();
            // Print a message to the LCD.
            //lcd.print("# of HITS");
 
            // start the countdown...
            Serial.println( "Program starts in 10 seconds" );
            delay( 100 );

            // reset the shooting gallery...
            resetSwitchVal = LOW;
            TAFS = TAFS_initial;
            NumberOfHits = 0;
            NumberOfShots = 0;
            elapsedTotalTime = 0;
            elapsedTargetTime = 0;
            beginningTotalTime = millis(); // beginning time in milliseconds
            beginningTargetTime = 0; // beginning target time in milliseconds
        }
    }
    while ( continueProgram == false );
    // end of do while ( continueProgram == false ); ==========

    Serial.print( "Time Allowed For Shot: " );
    Serial.print( TAFS );
    Serial.print( "  -  Elaspsed Total Time: " );
    Serial.print( elapsedTotalTime );
    Serial.print( "  -  Total Time Allowed: " );
    Serial.println( TTA );

    // begin while elapsedTotalTime is less than TTA ( TotalTimeAllowed ) ==========
    while ( elapsedTotalTime < TTA )
    {
        // Select a target index between 0 and 4 - max value of function is not inclusive
        do
        { 
            randomTarget = random( 0 , number_of_targets );
        }
        while ( ( randomTarget == previous_randomTarget_1 ) || ( randomTarget == previous_randomTarget_2 ) );
        previous_randomTarget_2 = previous_randomTarget_1;
        previous_randomTarget_1 = randomTarget;

        // raise target
        Serial.print( "Turn on target on pin: " );
        Serial.println( targetPin[ randomTarget ] );
        digitalWrite( targetPin[ randomTarget ] , LOW );
    
        // initialize beginningTargetTime
        beginningTargetTime = millis();
        elapsedTargetTime = ( millis() - beginningTargetTime );
        //    Serial.print( "beginingTargetTime = " );
        //    Serial.println( beginningTargetTime );
        //    Serial.print( "TAFS = " );
        //    Serial.println( TAFS );
        //    Serial.print( "elapsedTargetTime = " );
        //    Serial.println( elapsedTargetTime );
        //    delay( 5000 );
        // loop while target is raised ==========
        while ( ( elapsedTargetTime < TAFS ) & ( elapsedTotalTime < TTA ) ) // PMCC NOTE ~ i think this should this be && instead of &...  && is boolean comparison...  & is bitwise comparison...
        {
            hit = digitalRead( switchPin[ randomTarget ] );
            Serial.println( hit );

            // Begin was target was hit? ==========
            if ( hit == HIGH ) 
            {
                digitalWrite( targetPin[ randomTarget ] , HIGH );
                Serial.print( "Target Hit!" );
                NumberOfHits = ++NumberOfHits;
                //lcd.setCursor( 11 , 0 );
                //lcd.println( NumberOfHits );
                break;
            }                
            // End was target was hit? ==========

            elapsedTargetTime = ( millis() - beginningTargetTime );
            elapsedTotalTime = ( millis() - beginningTotalTime );
            //Serial.print( "elapsedTargetTime = " );
            //Serial.println( elapsedTargetTime );
        }
        // end target raised loop ==========

        // lower target
        Serial.print( "Turn off target on pin: " );
        Serial.println( targetPin[ randomTarget ] );
        digitalWrite( targetPin[ randomTarget ] , HIGH );

        // compute new TAFS
        TAFS = ( TAFS * 0.8 );
        if ( TAFS < TAFS_Minimum )
        {
            TAFS = TAFS_Minimum;
        }
        Serial.print( "New Time Allowed For Shot: " );
        Serial.print( TAFS );
        elapsedTargetTime = 0;
        //elapsedTotalTime = millis() - beginningTotalTime;
        Serial.print( "  -  Elapsed Total Time = " );
        Serial.print( elapsedTotalTime );
        Serial.print( "  -  Total Time Allowed: " );
        Serial.println( TTA );
    }  // end of while loop
    // end while elapsedTotalTime is less than TTA (TotalTimeAllowed) ==========

    Serial.print( "Total number of hits: " );
    Serial.println( NumberOfHits );

    // Pause 5 seconds
    delay( 5000 );

    // Raise all targets
    Raise_Lower_All_Targets( "Raise" );

    continueProgram = false;

}
//end loop()  ==========
