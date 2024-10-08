# Paradigmas de Programación

- Evaluaciones
    - 4 Mejores Laboratorios 30% **(N ≥ 4)**
        - Lab 1 Jueves 22 agosto
        - Lab 2 Jueves 12 septiembre
        - Lab 3 Jueves 3 octubre
        - Lab 4 Jueves 24 octubre
        - Lab 5 Jueves 14 noviembre
    - Tarea 1 25%
        
        EP1 Jueves 29 agosto (Presencial)
        
        EF1 miércoles 11 septiembre
        
    - Tarea 2 25%
        
        EP2 Jueves 10 octubre (Presencial)
        
        EF2 miércoles 23 octubre
        
    - Tarea 3 20%
        
        EP3 Martes 19 noviembre (Presencial)
        
        EF3 Martes 3 diciembre
        
        Evaluación Martes de diciembre
        
- Clases
    - Clase 1 30/07
        - 
    - Clase 2 06/08
        - Programacaion Orientada a Objetos
        - Todo objeto pertenece a una clase: Hay fantasmas (Clase) en el pacman de distintos colores (Objeto)
        - Se crea un objeto perteneciante a una clase
- Tarea 1
    - **2.1  Robot**
        - Nombre
        - Energía máxima
        - Conjunto de ataques
            - Nombre del ataque
                
                
            - Características del ataque
                - Type
                    - Long
                    - Sword
                    - Hand
                - Objective
                    - Robot
                    - Team
                - Damage
                    - Amount of damage. **X > 0**
                - Precision
                    - Percentage that indicates how many times the attack achieves its goal.   **10 < X <100**
                - Recharge
                    - How many turns to wait before being able to use this attack again. For example, if the value is 0 it means there is no wait and the attack can be used at any time. turn, while if the value is 2, then it means that after using this attack you have to wait 2 attack turns before you can use it again.
            
            ```python
            class Attack:
                def __init__(self, name: str, attack_type: str, objective: str, damage: int, precision: int, recharge: int):
                    self.name = name
                    self.type = attack_type
                    self.objective = objective
                    self.damage = damage
                    self.precision = precision
                    self.recharge = recharge
                    self.cooldown = 0
            
            #La clase Attack considera para cada ataque un nombre, tipo, objetivo, daño, precision y recarga
            ```
            
        - Conjunto de habilidades
            - Nombre de la habilidad
                
                
            - Características de la habilidad
                - Trigger
                    - indica que debe ocurrir durante la batalla para que la habilidad se active. Existirán distintos mecanismos de activación:
                        - energy → activación cuando la energía del robot baja de un cierto umbral.
                        - attack type → activación al recibir un tipo de ataque determinado.
                        - attack team → activación ante ataques cuyo objetivo es el equipo.
                        - turns → activación luego de una cierta cantidad de turnos de ataque.
                - Duration
                    - se refiere a la cantidad de rondas en que la habilidad estará activa.
                - Objective
                    - Robot
                    - Team
                - Effect
                    - los efectos de la habilidad pueden ser tanto en ataque como en defensa:
                        - shield type → reducción del daño de un tipo de ataque en un porcentaje definido.
                        - steroids → aumento en un porcentaje definido del daño de un tipo de ataque definido.
                        - hawk → aumento en un porcentaje de la precisión de un tipo de ataque.
                        - shifting → reducción de la precisión de un tipo de ataque recibido en un porcentaje definido.
                        - fast recharge → eliminar los turnos de recarga.
                        - magic → aumento de una cierta cantidad de energía.
            
            ```python
            class Skill:
                def __init__(self, name: str, trigger: str, trigger_value: int, duration: int, objective: str, effect: str, effect_value: int):
                    self.name = name
                    self.trigger = trigger
                    self.trigger_value = trigger_value
                    self.duration = duration
                    self.objective = objective
                    self.effect = effect
                    self.effect_value = effect_value
                    self.active = False
                    self.remaining_duration = 0
            ```
            
        
        ```python
        
        class Robot:
            def __init__(self, name: str, energy: int, attacks: List[Attack], skills: List[Skill]):
                self.name = name
                self.max_energy = energy
                self.current_energy = energy
                self.attacks = attacks
                self.skills = skills
        
        **#el Robot tiene nombre, energia, ataques y habilidades** 
        
            def select_attack(self) -> Attack:
                available_attacks = [attack for attack in self.attacks if attack.cooldown == 0]
                if not available_attacks:
                    return None
                return random.choice(available_attacks)
                
        #Selecciona un **ataque disponible** al azar        
        
            def receive_damage(self, damage: int):
                self.current_energy = max(0, self.current_energy - damage)
                
        #Reduce la energia del robot cuando **recive daño**        
        
            def is_defeated(self) -> bool:
                return self.current_energy <= 0
                
        #El robot pierde cuando su energia es menor a 0       
        
            def activate_skills(self, trigger: str, value: int = None):
                for skill in self.skills:
                    if skill.trigger == trigger:
                        if trigger == "energy" and self.current_energy <= skill.trigger_value:
                            skill.active = True
                            skill.remaining_duration = skill.duration
                        elif trigger == "turns" and value == skill.trigger_value:
                            skill.active = True
                            skill.remaining_duration = skill.duration
                            
        #El robot puede activar una habilidad                      
        
            def update_skill_durations(self):
                for skill in self.skills:
                    if skill.active:
                        skill.remaining_duration -= 1
                        if skill.remaining_duration == 0:
                            skill.active = False
        #Con esta funcuion se actualza la duracion de la habilidad
        
            def reset_for_battle(self):
                self.current_energy = self.max_energy
                for attack in self.attacks:
                    attack.cooldown = 0
                for skill in self.skills:
                    skill.active = False
                    skill.remaining_duration = 0
                    
        #Se resetean los parametros y energia del robot para la proxima batalla            
        ```
        
    - **2.2  Competición**
        - **Todos los tipos de competiciones pueden ser entre robots o entre equipos de robots.**
        - **Liga** → todos batallan contra todos. Se genera una tabla de posiciones en base a la cantidad de victorias, en caso de empate en victorias, quedara arriba aquel que haya ganado la batalla entre los robots/equipos empatados.
        - **Playoff** → batallas de eliminación directa, el que gana la batalla continua en competición avanzando a la siguiente ronda. Así hasta que queden dos, los cuales disputan la final, siendo campeón quien gana esa batalla.
        - **Torneo** → se hacen grupos en los cuales batallan todos contra todos, clasificando los dos mejores a unos playoff. El tamaño de los grupos es configurable entre 3 y 8.
    - **2.3  Batalla**
        - Las batallas son en modo de turnos, donde uno de los contrincantes parte realizando su primer ataque, y luego le corresponde al otro.
        - El ataque a realizar debe ser escogido aleatoriamente entre los ataques disponibles del robot/equipo.
        - En el caso de batallas por equipo, aplica la restricción de que no se puede utilizar en dos turnos seguidos ataques del mismo robot.
        - Una vez se escoge el ataque este debe ser aplicado al robot o equipo contrincante.
        - Antes de aplicar el daño se debe revisar si se activa alguna habilidad producto de este ataque, así como revisar si las habilidades activas modifican los daños del ataque recibido.
        - De esta forma, es importante que las habilidades se activan previo a calcular el daño, y que ese turno se considera como el primer turno donde la habilidad se encuentra activa
        - Cuando un robot se queda sin energía, ese robot no puede seguir participando en la batalla, incluidas sus habilidades.
        - La batalla la gana quien logre sacar de la partida a todos los robots contrincantes.
        - Antes de comenzar una batalla, todos los robots participantes vuelven a su nivel máximo de energía.
    - **2.4  Modos de Juego**
        - **Automático** → se realiza la competición de forma automática, se muestra en la consola cada partida con sus resultados, siguiendo una batalla después de otra hasta finalizar.
        - **Participativo** → el usuario escoge un robot/equipo al cual controlar, de tal forma que ser´a el
        usuario quien escoja los ataques de su competidor. En las batallas donde participe el robot/equipo del usuario, se deberá mostrar en consola el estado de todos los robots participantes, indicando la energía que les queda, y las habilidades que se encuentran activadas. Cuando se utiliza este modo, el usuario podrá guardar la partida para continuarla después (esto implica que puede terminar el programa y continuarla una próxima vez que lo ejecute, sin perder información).
    - **2.5  Reportes**
        - El usuario podr´a definir previo a ejecutar una competici´on, los reportes que quer´a visualizar al finalizar la competici´on. Estos reportes podrían ser gráficos (guardados como imagen) o archivos en formato CSV que representen tablas. Es así como deberán estar disponibles los siguientes reportes:
            - Tabla de victorias y derrotas por robot/equipo, incluyendo la cantidad de turnos totales disputados.
            - Para cada robot/equipo, un grafico de barras con la cantidad de veces que se utilizo cada ataque durante la competición.
            - Para cada robot/equipo, un grafico de barras con la cantidad de veces que se activo cada habilidad.
            - Tabla con cada batalla disputada, indicando el ganador, la cantidad de turnos, y la energía total con la que quedan el ganador de la batalla.