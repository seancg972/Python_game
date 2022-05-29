# Python_game


from ast import Break
from random import randint

class Attack:
  def __init__(self,name,dmg,crit_chance,miss_chance, effect = None):
    self.name = name
    self.dmg = dmg
    self.crit_chance = crit_chance
    self.miss_chance = miss_chance
    self.effect = effect
  
  def calculate_damage(self):
    from random import randint
    R = randint(0,100)
    if R < self.crit_chance:
      return self.dmg * 2

    R = randint(0,100)
    if R < self.miss_chance:
      return 0
    
    return self.dmg

class attack_magic(Attack):
  def __init__(self,name,dmg,crit_chance,miss_chance,mana_cost, effect = None):
    super().__init__(name,dmg,crit_chance,miss_chance, effect)
    self.mana_cost = mana_cost
  
  def calculate_damage(self,player):
    if player.mana >= self.mana_cost:
      player.mana -= self.mana_cost
      return super.calculate_damage()
    return 0

class status:
  def __init__(self,name,effect,duration):
    self.name = name
    self.effect = effect
    self.duration = duration
  
  def update_entity(self,entity):
    if self.effect == "Stun" and self.duration <= 0:
      entity.can_play = True
      return

    if self.effect == "Poison":
      entity.hp -= 1
    elif self.effect == "Regen":
      entity.hp += 1
    elif self.effect == "Stun":
      entity.can_play = False
    elif self.effect == "Berserk":
      entity.strength += 1
    self.duration -= 1

class Entity:
  def __init__(self,name,hp,strength,defense):
    self.name = name
    self.hp = hp
    self.mana = 100
    self.strength = strength
    self.defense = defense
    self.inventory = []
    self.attack_list = []
    self.equipped_weapon = None
    self.critical_chance = 1
    self.money = 0
    self.list_status = []
    self.can_play = True
    self.xp = 0

  def damage_suffered(self,damage_suffered):
    self.hp -= damage_suffered
   
  def attack(self,target_player):
    target_player.damage_suffered(self.strength)

  def take_damage(self,amount):
    if amount > self.defense:
      amount -= self.defense
      self.hp -= amount 

  def equip(self, id_weapon):
    if self.equipped_weapon != None:
      self.desequip(self.equipped_weapon)

    self.equipped_weapon = self.inventory[id_weapon]
    self.strength += self.inventory[id_weapon].strength_bonus

  def desequip(self, weapon):
    self.strength -= weapon.strength_bonus

  def level_up(self):
    self.strength += 2
    self.defense += 2
    self.mana = 100
    self.hp = 100

class Merchant(Entity):
  def __init__(self,items,name,money):
    super().__init__(name,100,10,10)
    self.money = money
    self.inventory = items
    self.attack_list.append(Attack("Fusil",50,25,20))
  
  def buy_item(self,player):
    for i in range(len(self.inventory)):
      print(i,"-",self.inventory[i].name,":",self.inventory[i].price)
    print("choisissez un item")
    choice = int(input())
    Item = self.inventory[choice]
    if player.money >= Item.price:
      player.money -= Item.price
      self.money += Item.price
      player.inventory.append(Item)
      self.inventory.remove(Item)
    else:
      print("Vous n'avez pas assez d'argent")

class Monster(Entity):
  def __init__(self,monster_type):
    if monster_type == "Corbin":
      super().__init__(monster_type,80,5,2)
    elif monster_type == "Minions":
      super().__init__(monster_type,90,2,2)
    elif monster_type == "Super Corbin":
      super().__init__(monster_type,100,20,30)
    elif monster_type == "Super Minions":
      super().__init__(monster_type,250,5,30)
    elif monster_type == "Golem":
      super().__init__(monster_type,150,10,2)
    elif monster_type == "RED":
      super().__init__(monster_type,10,2,2)

  def Loot(self):
    from random import randint
    return self.inventory[randint(0,len(self.inventory))]

class Player(Entity):
  def __init__(self,name,type_adventurer):
    self.type_adenturer = type_adventurer
    if type_adventurer == "Garen":
      super().__init__(name,400,20,20)
      self.inventory.append(Weapon("Great_Sword",20,5,1))
      self.inventory.append(Item("Potion de soin","heal",50,5))
    elif type_adventurer == "Kayn":
      super().__init__(name,450,40,10)
      self.inventory.append(Weapon("Dagger",5,20,1))
      self.inventory.append(Weapon("Sword",10,5,5))
      self.inventory.append(Weapon("Staff",3,50,3))
      self.inventory.append(Item("Potion de soin","heal",50,5))
    elif type_adventurer == "Irelia":
      super().__init__(name,450,40,10)
      self.inventory.append(Item("Potion de soin","heal",50,5))

      
  def open_inventory(self):
    for i in range(len(self.inventory)):
      print(i,":",self.inventory[i].name)
    print("Quel objet voulez vous utiliser ?")
    choice = int(input())
    item = self.inventory[choice]
    if item == Weapon:
      self.equip(choice)
    elif item == Item:
      item.use(self)


class Item:
  def __init__(self,name,effect,power,price):
    self.name = name
    self.effect = effect
    self.power = power
    self.price = price

  def use(self,target):
    if self.effect == "heal":
      target.hp += self.power
    elif self.effect == "Str":
      target.strength += self.power

class Weapon(Item):
  def __init__(self, name, strength_bonus, critical_chance, price):
    super().__init__(name,"Str",strength_bonus, price)
    self.critical_chance = critical_chance

  def use(self,target):
    super().use(target)
    target.critical_chance += self.critical_chance


#Creation des oblets du jeu







#Ecriture du scénario

print("Oh oh salut à toi grand joueur")
dialogue_a = input("")
#Appuyer sur Entrer pour passer au dialogue suivant
if dialogue_a == "":
  print("Je te souhaite la bienvenue dans le monde de League Of Legends")
dialogue_b = input("")
#Appuyer sur Entrer pour passer au dialogue suivant

if dialogue_b == "":
  print("Combat dans ce monde jusqu'à monter l'étage 100")
dialogue_c = input("")
#Appuyer sur Entrer pour passer au dialogue suivant
if dialogue_c == "":
  print("Je te souhaite bon courage")
dialogue_d = input("")
#Appuyer sur Entrer pour passer au dialogue suivant

if dialogue_d == "":
  print("Veuillez choisir votre Champion?")
dialogue_e = input("")
#Appuyer sur Entrer pour passer au dialogue suivant
if dialogue_e == "":
  print("Voici les champions disponible")
dialogue_f = input("")
#Appuyer sur Entrer pour passer au dialogue suivant

if dialogue_f == "":
  print("Kayn\n Garen\n Irelia\n")
    

type = input("Choisir le type de votre joueur ? : ")
nom = input("Choisis ton nom ? : ")

      
Joueur = Player(nom,type)
print("Bienvenue, dans le labyrinte aux 100 étages, vous devez battre le boss à chaque fois pour passer aux niveaux suivants")




#Message de Bienvenue au Joueur


#Code Du jeu 

#Etage 1

Premier_etage = False
Game_over_1 = False
Compteur_Boss_1 = 0
boss_1 = False

while Premier_etage == False and Game_over_1 == False:

  route = input("Quelle route voulez vous prendre ? Gauche , Droite , Devant , Derrière ou taper sac pour ouvrir l'inventaire : ")

  if Compteur_Boss_1 == 9:
    boss_1 = True


  if route == "sac":
    Joueur.open_inventory()

  if route == "Gauche":
    ran = randint(1,4)

    #Après qu'il ai tuer le corbin, le Champions gagne des dégats
    if ran == 1:
      Monstre = Monster("Corbin")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp > 0 and Monstre.hp > 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()
      
    if ran == 2:
      print("Vous êtes dans une zône neutre")
      print("Il ne se passe rien")
    
    if ran == 3:
      print("Vous rencontrez le Marchand")

    #Après qu'il ai tuer le minions, le Champions gagne des dégats

    if ran == 4:
      Monstre = Monster("Minions")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp > 0 and Monstre.hp > 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()    

  if route == "Droite":
    ran = randint(1,4)

    #Après qu'il ai tuer le corbin, le Champions gagne des dégats
    if ran == 1:
      Monstre = Monster("Corbin")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp > 0 and Monstre.hp > 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()
      
    if ran == 2:
      print("Vous êtes dans une zône neutre")
      print("Il ne se passe rien")
    
    if ran == 3:
      print("Vous rencontrez le Marchand")

    #Après qu'il ai tuer le minions, le Champions gagne des dégats

    if ran == 4:
      Monstre = Monster("Minions")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp >= 0 and Monstre.hp >= 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()   


  if route == "Devant":
    ran = randint(1,4)

    #Après qu'il ai tuer le corbin, le Champions gagne des dégats
    if ran == 1:
      Monstre = Monster("Corbin")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp >= 0 and Monstre.hp >= 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()
      
    if ran == 2:
      print("Vous êtes dans une zône neutre")
      print("Il ne se passe rien")
    
    if ran == 3:
      print("Vous rencontrez le Marchand")

    #Après qu'il ai tuer le minions, le Champions gagne des dégats

    if ran == 4:
      Monstre = Monster("Minions")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp >= 0 and Monstre.hp >= 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()   

  if route == "Derrière":
    #Après qu'il ai tuer le corbin, le Champions gagne des dégats
    if ran == 1:
      Monstre = Monster("Corbin")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp >= 0 and Monstre.hp >= 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()
      
    if ran == 2:
      print("Vous êtes dans une zône neutre")
      print("Il ne se passe rien")
    
    if ran == 3:
      print("Vous rencontrez le Marchand")

    #Après qu'il ai tuer le minions, le Champions gagne des dégats

    if ran == 4:
      Monstre = Monster("Minions")
      print("Vous rencontrer un monstre féroce , il vous attaque")
      while Joueur.hp >= 0 and Monstre.hp >= 0:
        print("Vos PV : ")
        print(Joueur.hp)
        print("PV du monstre : ")
        print(Monstre.hp)
        action = input("Voulez-vous attaquez où ouvrir votre sac ? : ")
        if action == "attaquez":
          print("Vous attaquez le Monstre")
          Joueur.attack(Monstre)
          if Monstre.hp > 0:
            Monstre.attack(Joueur)
            if Joueur.hp <= 0:
              Game_over_1 = True
          if Monstre.hp <= 0:
            print("Vous avez tuer le monstre")
            Compteur_Boss_1 += 1 
            Joueur.strength += 3
        if action == "sac":
          Joueur.open_inventory()   

  if boss_1 == True:
    Golem = Monster("Golem")
    print("Vous rentrez dans la salle du Boss du premier étages\n")
    print("hahha Jeune Aventurier insolent votre vie prendra fin")
    
    while Joueur.hp >= 0 and Golem.hp >= 0:
      print("Vos PV : ")
      print(Joueur.hp)
      print("PV du monstre : ")
      print(Golem.hp)
      action = input("Voulez-vous attaquez ? : ")
      if action == "attaquez":
        Joueur.attack(Golem)
      if Golem.hp > 0:
        Golem.attack(Joueur)

    if Joueur.hp <= 0:
      Game_over_1 = True
      
    
    if Golem.hp <= 0:
      Premier_etage = True
      Joueur.strength += 10



#Etage 2

Second_etage = False
Game_over_2 = False

if Game_over_1 == True:
  print("Vous avez perdu aux Premier étages")
elif Premier_etage == True:
  while Second_etage == False and Game_over_2 == False:
    print("La suite seras disponible les **/**/2022, meric à vous d'avoir tester la version alpha du jeu")
    break



