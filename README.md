# Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip # klipper # mmu # ervf v2 #
Pointe de coupe du servomoteur de direction Stealthburner-CW2

bilibili UID : 450728302

Par 3D Creator

![1708151775972fbb961577cd52aac32a4a684aa6e033fde65](https://github.com/tdft/Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip/assets/52322373/c964d3c7-3fd0-443d-9fc7-fa530b00edc1)

La nouvelle version de Happy-Hare peut être modifiée selon le tutoriel officiel https://github.com/moggieuk/Happy-Hare pour modification

Pour des tests spécifiques, voir Bilibili [Test de la tête de coupe servo Voron SB] https://www.bilibili.com/video/BV19A4m157Jf?vd_source=c7e963c712fcfe6c78b672060f20e54b

https://github.com/tdft/Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip/assets/52322373/de4dca90-7167-4381-bc9b-4ff3145389e5

Je décline toute responsabilité en cas d'accident (y compris, mais sans s'y limiter, les chocs électriques, les dommages électriques, etc.).

Avant d'installer le module, assurez-vous de couper l'alimentation et de démonter la machine afin d'éviter tout accident.

Servomoteur MG90S à vérifier !

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

N'ajoutez pas de ressort au bras de coupe ; la coupe revient à sa position initiale grâce au servomoteur !

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Préparation avant l'installation :

Plusieurs écrous thermofusibles M3 3x4x5

Une vis M3x12

Servo ES08MD x 1 (MG90S)

Fer à souder électrique

Jeu de vis CW2

Plus de 100 mm réservés à la ligne servo

Retirez le bloc de coupe avant la mise au point afin d'éviter d'endommager les composants.
![IMG_20240216_155934](https://github.com/tdft/Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip/assets/52322373/cd3275af-8d06-4722-98c8-d851bb7323f9)

Le fichier mmu/base/mmu_filametrix.cfg doit être modifié en fonction de la taille de la machine et de l'emplacement de nettoyage. Pour référence, uniquement

##########################################

#Ce paramètre doit être défini sur la distance entre l'extrémité de la buse intérieure et la lame de coupe. Il est utilisé dans les calculs ci-dessus.
#Position finale de l'extrémité du filament (output_stop_position) et taille du fragment de filament restant (output_remaining_filament).
#Utilisé par le Happy Hare
variable_blade_pos:37.5

#Distance de rétraction avant la coupe : cela réduit le gaspillage de filament, mais peut provoquer des bourrages.
#Si la valeur est trop grande et/ou qu'il y a du jeu dans l'extrémité de l'arbre,
#Ce paramètre doit être inférieur à « balde_pos », la distance entre la buse et le couteau.
variable_retract_length:32.5

#Emplacement de la goupille : cet emplacement doit être celui du couteau lorsque le porte-outil coupe.
#Appuyez doucement sur la goupille.
variable_pin_loc_x:165 #22
variable_pin_loc_y: 305

#Positions de départ et d'arrivée lors de la coupe
#Plus précisément, au lieu de couper en se déplaçant vers la position de la broche ci-dessus,
#Nous laissons une petite marge de sécurité le long de l'axe X pour éviter de rayer la broche lors de son déplacement.
#Cela devrait également laisser une petite distance pour générer un élan lors de la pression sur la broche.
variable_pin_park_x_dist:0 #9

#Position du porte-outil lorsque l'outil est complètement comprimé.
#Un faible jeu doit être laissé (par exemple, légèrement supérieur à 0 ou à la valeur xmin) pour éviter de heurter le porte-outil ou le portique.
variable_pin_loc_x_compressed: 165 #1

#Longueur et vitesse de rétraction après la coupe afin que l'outil puisse revenir en position initiale.
variable_rip_length: 1 #Dist de rétraction pour faciliter la décompression (>= 0).
variable_rip_speed: 750

#Pousser la pointe restante de l'extrémité froide vers l'extrémité chaude. Ne peut pas être supérieur à « retraction_length »
variable_pushback_length : 10
variable_pushback_dwell_time : 0 # temps de maintien de la poussée
########################################################################################

Installation

# ... mmu_gantry_servo]

Ouvrir
Paramètres pour les servomoteurs numériques ES08MD uniquement ! !
#Servo de portique en option pour broyeur FILAMETRIX -
#Paramètres PWM de base du servomoteur. Si ces valeurs changent, les angles définis pour les différentes positions changeront également.
#
#Ne commentez que si vous possédez un servomoteur à crémaillère.

########################################

[mmu_servo mmu_gantry_servo]
pin: EBBCan:PB9
maximum_servo_angle: 180
minimum_pulse_width: 0.0005
maximum_pulse_width: 0.0025
initial_angle: 130
##########################################

2.
Ouvrez mmu/base/mmu_filametrix.cfg et activez l'option servomoteur à crémaillère.

##############################################

#Si l'option servomoteur à crémaillère est installée, activez le servomoteur et définissez les angles de montée et de descente. positions
variable_gantry_servo_enabled: 1
variable_gantry_servo_down_angle: -5 #Coupe immédiate
variable_gantry_servo_up_angle: 130

############################################

*3. Facultatif
(Copiez et remplacez directement les paramètres dans mmu/base/mmu_filametrix.cfg)

########################################

[gcode_macro _FILAMETRIX_GANTRY_SERVO_DOWN]
description : Actionner la broche optionnelle actionnée par le servomoteur du portique
gcode :
{% if printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_enabled'] > 0 %}
{% set ANGLE = printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_down_angle'] | float %}
status_busy
SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
G4 P500
_FILAMETRIX_GANTRY_SERVO_UP
G4 P300
SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
G4 P200
_FILAMETRIX_GANTRY_SERVO_UP
SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
G4 P600
status_part_ready
{% endif %}

[gcode_macro _FILAMETRIX_GANTRY_SERVO_UP]
description : Actionner la broche optionnelle actionnée par le servomoteur du portique
gcode :
{% if printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_enabled'] > 0 %}
{% set ANGLE = printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_up_angle'] | float %}
SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
{% endif %}

######################################
