# Noita modding
This file is made by me (Absolute Goddus), in order to help me (and possibly you), to mod Noita.
I Will write down & document every function & feature I find here, since (at least of my knowledge) there is not much __new__ info on it.

 > Note that I will be using my own coding scheme in this tutorial / documentation, meaning that not everything I do is needed, but is simply do it as it improves something in some way. Also note that not everything I say may be right. I'm a cpp dev though, meaning I will hopefully not talk some nonsense. (Also sorry for my bad english, I'm German)

## Adding spells
To add spells in noita you should have the component_documentation ready, since it explains the components needed later.
1. Create an init.lua. This is where you will put any functions in that are required to be executed. If you already have one skip this step.
2. add:
```lua
ModLuaFileAppend( "data/scripts/gun/gun_actions.lua", "mods/<YOUR MOD NAME HERE>/files/actions.lua" )
```
Let's dissect this code.
```ModLuaFileAppend(dst, src)``` Appends the file 'src' to the file 'dst'.
What this means is that if the contents of the file src are: <br>```This File is called src!!!```, then calling ModLuaFileAppend (path/to/dst.lua, path/to/src.lua) will copy the entire file of 'src.lua' into 'dst.lua'
Now as you can tell we want to copy 'actions.lua' into 'gun_actions.lua' but why?
We do this because in the context of modding gun_actions.lua actually inserts elements into a table of spells (at least that's what I figure). Now using that code we can simply put all our spell registering code into actions.lua! Apart from that, the use of ModLuaFileAppend doesn't require ```request_no_api_restrictions="1"```, which is pretty important since it disables features such as the workshop, but it also makes other mods able to add their spells to since they are not going to get overwritten, by us or some other mod trying to directly modify the file.
3. Create actions.lua (this is where we will be adding most of our spells). it should be created in <YOUR MOD NAME HERE>/files/actions.lua
4. Write our spell code!!!<br>
Now Wri ting our spell code, itself requires a few steps. firstly we have to add an entry into our actions.lua. To do this add the following code to it:
```lua
table.insert( actions,
{
	id          = "STRONG_MAGIC_BOLT",
	name 		= "Heavy bolt",
	description = "A powerfull version of the simple magic bolt",
	sprite 		= "mods/<YOUR MOD NAME HERE>/files/actions/sea_swamp.png",
	type 		= ACTION_TYPE_MATERIAL,
	spawn_level                       = "0,4,5,6", -- BERSERK_FIELD
	spawn_probaability                 = "1,1,1,1", -- BERSERK_FIELD
	price = 350,
	mana = 50,
	action 		= function()
		add_projectile("mods/<YOUR MOD NAME HERE>/files/actions/strong_bolt.xml")
		c.fire_rate_wait = c.fire_rate_wait + 15
	end,
} )
```
(Now to be true this is almost ripped straight out of the example mod and I have no idea what --BERSERK_FIELD implies, but I will update this if I figure it out <!-- TODO: FIX_BERSERK_FIELD --> )
It's dissection time!
So first we have the ```id```. This is the internal ```id``` (used in code) that your projectile uses. I usually write them in uppercase, while replacing spaces with underscores. Secondly the ```name```. This is the cosmetic name (the name that will be shown in game). Thirdly there is the ```description```. This is a simple description for what your spell does. The ```sprite``` is the icon that your spell will use. here I simply use the sea_swamp from the example mod. <!-- TODO: ADD_BERSERK_FIELDS_EXPLANATION --> Since I don't currently know what ```spawn_level``` or ```spawn_probability``` does, I won't explain them (for now). the ```price``` & ```mana``` fields should be pretty self-explanatory. and lastly we have the ```action``` this is simply the code that actually is executed once a spell is fetched. Here we simply add our spell to the wand fired, and we are going to add some cast_delay.

Now when we add our projectile (```add_projectile("mods/<YOUR MOD NAME HERE>/files/actions/strong_bolt.xml")```), we link to an external .xml file, but why? This is simply because the spell's (and many other things) in Noita use a Component system. Now you won't need to learn .xml (At least as of my knowledge), but we will be using it a lot. To see the documentation of every single component see ```component_documentation.lua ```

To start lets create our first .xml file. To do that create a file in ```<YOUR MOD NAME/files/actions/``` called strong_bolt.xml. Now open the file & paste the following code into it:
```xml
<Entity name="$projectile_default" tags="projectile_player" >

	<Base file="data/entities/base_projectile.xml" >
    <VelocityComponent
      gravity_y="200"
      air_friction="1.7"
	  mass="0.04"
	  >
    </VelocityComponent>
	</Base>

  <ProjectileComponent
    _enabled="1"
    lob_min="0.5"
    lob_max="0.7"
    speed_min="300"
    speed_max="500"
    friction="1"
    direction_random_rad="0.00"
    on_death_explode="1"
    on_death_gfx_leave_sprite="0"
    on_lifetime_out_explode="1"
    explosion_dont_damage_shooter="1"
    on_collision_die="1"
    lifetime="40"
    damage="1"
    velocity_sets_scale="1"
    lifetime_randomness="7"
    ragdoll_force_multiplier="0.005"
    hit_particle_force_multiplier="0.1"
    muzzle_flash_file="data/entities/particles/muzzle_flashes/muzzle_flash_small_pink.xml"
    shoot_light_flash_r="255"
    shoot_light_flash_g="160"
    shoot_light_flash_b="255"
    shoot_light_flash_radius="64"
	knockback_force="0"
	>
    <config_explosion
      never_cache="1"
      damage="1"
      camera_shake="1"
      explosion_radius="6"
      explosion_sprite="data/particles/explosion_008_pink.xml"
      explosion_sprite_lifetime="0"
      create_cell_probability="0"
      hole_destroy_liquid="0"
      hole_enabled="1"
      ray_energy="400000"
      max_durability_to_destroy="6"
      particle_effect="0"
      damage_mortals="1"
	  physics_explosion_power.min="0.22"
      physics_explosion_power.max="0.3"
      physics_throw_enabled="1"
      shake_vegetation="1"
      sparks_count_max="20"
      sparks_count_min="7"
      sparks_enabled="1"
      material_sparks_enabled="1"
      material_sparks_count_max="2"
      material_sparks_count_min="0"
      light_enabled="0"
      stains_enabled="1"
      stains_radius="3" >
    </config_explosion>
  </ProjectileComponent>

  <SpriteComponent
    _enabled="1"
    alpha="1"
    image_file="data/projectiles_gfx/spark.xml"
    next_rect_animation="default"
    rect_animation="default"
	additive="1"
     >
  </SpriteComponent>

  	<!-- dense emitter -->
	<ParticleEmitterComponent
		emitted_material_name="plasma_fading_pink"
		offset.x="0"
		offset.y="0"
		x_pos_offset_min="0"
		x_pos_offset_max="0"
		y_pos_offset_min="0"
		y_pos_offset_max="0"
		x_vel_min="0"
		x_vel_max="0"
		y_vel_min="0"
		y_vel_max="0"
		gravity.y="0.0"
		count_min="1"
		count_max="4"
		lifetime_min="0.1"
		lifetime_max="0.2"
		is_trail="1"
		trail_gap="1.0"
		render_on_grid="1"
		fade_based_on_lifetime="1"
		airflow_force="10.5"
		airflow_time="1.401"
		airflow_scale="0.05"
		emission_interval_min_frames="1"
		emission_interval_max_frames="1"
		emit_cosmetic_particles="1"
		create_real_particles="0"
		is_emitting="1" >
	</ParticleEmitterComponent>

  	<!-- sparse emitter -->
	<ParticleEmitterComponent
		emitted_material_name="plasma_fading_pink"
		offset.x="0"
		offset.y="0"
		x_pos_offset_min="0"
		x_pos_offset_max="0"
		y_pos_offset_min="0"
		y_pos_offset_max="0"
		x_vel_min="20"
		x_vel_max="40"
		y_vel_min="0"
		y_vel_max="0"
		gravity.y="0.0"
		count_min="1"
		count_max="2"
		lifetime_min="0.6"
		lifetime_max="0.8"
		is_trail="0"
		trail_gap="8.0"
		render_on_grid="1"
		fade_based_on_lifetime="1"
		airflow_force="1.5"
		airflow_time="0.401"
		airflow_scale="0.05"
		emission_interval_min_frames="1"
		emission_interval_max_frames="10"
		emit_cosmetic_particles="1"
		create_real_particles="0"
		is_emitting="1" >
	</ParticleEmitterComponent>

  	<!-- very sparse emitter -->
	<ParticleEmitterComponent
		emitted_material_name="plasma_fading_pink"
		offset.x="0"
		offset.y="0"
		x_pos_offset_min="0"
		x_pos_offset_max="0"
		y_pos_offset_min="0"
		y_pos_offset_max="0"
		x_vel_min="10"
		x_vel_max="20"
		y_vel_min="0"
		y_vel_max="0"
		gravity.y="0.0"
		count_min="1"
		count_max="1"
		lifetime_min="1.4"
		lifetime_max="1.5"
		is_trail="0"
		trail_gap="8.0"
		render_on_grid="1"
		fade_based_on_lifetime="1"
		airflow_force="0.5"
		airflow_time="0.5"
		airflow_scale="0.05"
		emission_interval_min_frames="10"
		emission_interval_max_frames="20"
		emit_cosmetic_particles="1"
		create_real_particles="0"
		is_emitting="1" >
	</ParticleEmitterComponent>

	<LightComponent
    _enabled="1"
	r="255"
	g="40"
	b="255"
    radius="30" >
  </LightComponent>

  <AudioComponent
      file="data/audio/Desktop/projectiles.bank"
      event_root="player_projectiles/bullet_light">
  </AudioComponent>

	<VariableStorageComponent
		name="projectile_file"
		value_string="data/entities/projectiles/deck/light_bullet.xml"
		>
	</VariableStorageComponent>

</Entity>
```
Though I won't explain what the components do (since they are documented in component_documentation.txt), I will explain the basics of what's going on. first you see that .xml uses tags (our in Noita's case they're mostly called components). This simply means that every entry has an opening tag (<entity>) & a closing tag (</entity>). Now to passing components simply put tags inside of tags e.g.
```xml
<tag>
	<component>

	</component>
</tag>
```
The last step us to passing the values of the components. This is simply done by writing the name of the value followed by an ```= "<DESIRED VALUE HERE>"``` e.g.
```xml
<entity name="SomeName">
	<component someValue="10">
    </component>
</entity>
```
Though in my coding standard it will look more like:
```
<entity
	name="SomeName">
	<component
		someValue="10">
    </component>
</entity>
```
Now if you have done everything right (and also include your image) you can run your code. (see the Noita wiki on a basic tutorial for that. I'm btw. using Jetbrains Fleet, which I strongly recommend, though every text editor will suffice (yes, even the Windows Editor))