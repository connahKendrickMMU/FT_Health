# FT_Health
This focus topic is based on a modular health system.
## Overview
This project demonstrates a simple and modular health system that can be applied to both players and enemies in a game. The health system is designed to be easily extendable, allowing for different types of health behaviors, such as basic health and armored health.

In this task you will revise the use of inheritance and polymorphism in C++ to create a flexible health system. Inaddition to an introduction on how to use pointers in C++.

## Where to find the relevant scripts

The scripts are located in the usual location for unreal C++ Classes->HealthDemo. 

THe relevant Blueprint are located in the Content->Blueprints folder. 

## Task

- First you should see how inheritance has been used to create a Armoured Health script that inherits from the base Health script. Extend this so it takes in some damage to armour.
- Secondly, Using pointers have the Armoured Enemies change colour when their armour is broken.

## Hints

### Armoured Health and Inheritance

Here is the Armoured Health header file, you can see by **using : UHealth it inherits** all the properties and functions of the base **Health class**. Likewise, I take the **TakeDamage function and override it** to add my own functionality.

ArmouredHealth.h:

![You can see the structure of the header file here.](Hints/ArmouredHealthHeader.png)

This also allows the **projectile to use polymorphism** to call the correct TakeDamage function depending on what type of health component it hits. So you don't need to adjust the projectile script (HealthDemoProjectile.cpp).

You will need to **add the variables and make use TakeDamage** function to implement the armour functionality. In the **ArmouredHealth.cpp**, Note the
```
Super::TakeDamage(Damage);
```
Calls the parent (UHealth) TakeDamage function.

### Armoured Foe
Armoured foes inherit from the basic foe class, you will see i dont even need to adjust the Health Script to armoured in code:

![You can see the type health is set to armoured Health.](Hints/HealthToArmouredHealth.png)

This is because the **basic foe's health component can store the type armoured health through polymorhism**.  

## Changes from base project

### HealthDemoProjectile.h 

I have modified the script that controls the projectile, add a function to handle the collision and store a damage amount.  
In DemoHealthProjectile.h Lines 26-32
```
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Damage, meta = (AllowPrivateAccess = "true"))
int Damage = 50;

/** called when projectile hits something */
UFUNCTION()
void OnHit(UPrimitiveComponent* HitComp, AActor* OtherActor, UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit);
```

### HealthDemoProjectile.cpp 

I the CPP file i have implemented the OnHit function, it checks to see if the other actor has some form of UHealth and if it does call the function, lines 35-56. The main code is:
```
UHealth* OtherHealth = Cast<UHealth>(OtherActor->GetComponentByClass(UHealth::StaticClass()));

if (OtherHealth != nullptr)
{
	UE_LOG(LogTemp, Warning, TEXT("OtherHealth hit"));
	OtherHealth->TakeDamage(Damage);
}
``` 
The Script also checks for if Physics is enabled and adds a force if it is, so the blue cubes act as normal.


### Basic Foe and Armoured Foe

I have create two classes the Basic Foe and the armoured foe, which inherits from the basic foe. The armoured foe has just one change that is the inclusion of a pointer (uses the *) towards the undamaged material: 
ArmourFoe.h lines 23-34
```		
// for this task look at how we called the died and see if you can recreate it, to change the material
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Material", meta = (AllowPrivateAccess = "true"))
UMaterial* UnArmouredMaterial;
```
Try to follow the code for the damging the armour first, before you try changing the material. Remember to interact with a point you need to use
```
// ->
if(UnArmouredMaterial)
{
	UnArmouredMaterial->(A Method to call);
}
```

### Pointers and materials
In Unreal certian type have to be pointers (uses the * symbol), mainly for us any thing begining with a U e.g. UMaterial, UStaticMesh.

Remember our BasicFoe has a body Static Mesh Component that we can access to change the material. You will have to create a form of comunication between the Armoured Health and the Armoured Foe to change the material when the armour is broken, you could try a few different ways. Personally, opening up a new delegate that is exclusively for armour breaking, would be more flexible. As then you could restore the Armour and break it later on again.

Ether way you will need to add a function to the Armoured Foe that will change the material of the body mesh to the unarmoured material when called, you must first do this in the header file:
```
// function to change material when armour is broken
UFUNCTION()
void ArmourBroken();
```
Then implement the function in the CPP file:
```
void AArmouredFoe::ArmourBroken()
{
	if (UnArmouredMaterial)
	{
		Body->SetMaterial(0, UnArmouredMaterial);
	}
}
```


## Challenges
Test your might

## Easy 
- Apply the base Health Script to the player and have it so enamies will damage the player on collision, see the projectile for an example.
- Add resistance to the armour so bullets do less damage to armour.
- Add a bit of randomisation to the damage dealt to health and armour.

## Medium
- Create a Health Pickup that will restore health to the player or enamies when collected.
- Create a Armour Pickup that will restore Armour to the player or armoured enemies when collected.
- Create a Super Pickup that will restore both Health and Armour to the player or enemies when collected.

## Hard
- Add feedback to the player and when an enemy takes damage, mainly a little hop when they are hit and a sound, see the weapon for sound example.
- Try to refine the mechanic, in your own unique way, such as slow regenerating health, temporary invincibility, shields (Halo, do you allow sponge, chip or breaking), permanent damage, health overcharging and drains, vampire health drain or damage over time effects.
- Refine our health to allow for respawning after a delay. 
- Possess our cubes and let them move towards the player in a basic manner.

# Reference