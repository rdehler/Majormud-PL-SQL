__Install Instructions__
Author: Raybdbomb <raybdbomb@gmail.com>
Version: 0.2
Last Updated: 2008-04-03

_ Changelog _
0.2:
 * Fixed a small bug with the update_* functions to use the correct second parameter.
0.1:
 * Initial version.

_ Intro _
This guide will lead you through installing Worldgroup, Majormud and the Pervasive SQL upgrade.

_ Preamble and Credits _
Ghaleon for his discovery and work in this.
Syntax for his work on Nightmare, MME and mudinfo.net.
Everyone at FHQ.

This guide assumes you use c:\wgserv, c:\xampp as your install directories, 192.168.1.103 is your IP.  change as necessary.

_ Setup WG and Majormud _
download torrent: http://thepiratebay.org/tor/3969596
Unzip Worldgroup 3.30.zip
Run KEYGEN.EXE, put in 8 digit registration number, click Generate.  Copy Activation code.
Run setup.exe, put in activation code instead of DEMO when requested.
Unrar MajorMUD v1.11p.rar, install setup.exe
Run c:\wgserv\wgsrunmt.exe.  Add a new Menu item called MUD.  Go to the page, make MUD a module page and have it point to the MajorMUD module.
Unrar MajorMUD v1.11p keygen.rar, run keygen.exe.
Select (1) MajorMUD Activation Code Generator, put it in 
Start => All Programs > Worldgroup Server (Common) => Utilities => Security & Accounting => WCCMMUD => Activate
Start up WorldGroup server.
Telnet to your server, go into Mud.
go to the SYSOP menu.
In keygen.exe, go to (4) MajorMUD Addon Activation Auto Generator.  Get your Reg# from sys lance.
For each addon code, in the SYSOP menu select A - Addon module activation codes, type the module number and put in the code.
In mud Sys go rhu (for example) to test.

_ Integrating with PSQL _
Download PSQL 9.5 Windows Server trial here (signup required) http://ww2.pervasive.com/Database/Trials/Pages/v9EngineforWindowsServer.aspx
Apply permanent key License QLGKJVWDXTW8C28TVF5CBE5F.
open Pervasive.SQL Control Center
In the left tab, under Engines, under your hostname, right click on Databases, select New -> Database
Database Name: mud, Location C:\wgserv, everything else default

_ Setup XAMPP _
Download XAMPP from its official site http://www.apachefriends.org/en/xampp-windows.html
Install it

_ Configure tables _
Create a file config.php, put it in c:\xampp\config.php
	<?php
	error_reporting(E_NONE);
	class db {
	
		var $connection;
		var $results;
		var $queries = array();
		
		function db($driver = "", $user = "", $pass = ""){
			$this->connection = odbc_connect($driver,$user,$pass); 
			if(!$this->connection){
				die("Unable to connect to database");
			}
		}
	
		function dbexec($q){
			return $this->query($q);
		}
		
		function query($q){
			$ret = true;
			$this->results = null;
			$this->results = odbc_exec($this->connection, $q);
			if(odbc_error()){
				//echo "Query failed: $q<br /><br />Error:<br/>".odbc_error();
				$ret = false;
			}
			$this->queries[] = $q;
			return $ret;
		}
		
		function showResults(){
			var_dump($this->results);
		}
		
		function fetchArray(){
			$obj = array();
			while($row = odbc_fetch_object($this->results)){
				$obj[] = $row;
			}
			return $obj;
		}
		
		function dump(){
			echo "<pre>";var_dump($this->fetchArray());echo "</pre>";
		}
		
		function dbtables(){
			$this->query('SELECT Xf$Name FROM x$file where Xf$Flags = 0;');
			$tables = array();
			while($row = odbc_fetch_object($this->results)){
				$tables[] = trim(current($row));
			}
			return $tables;
		}
		
		function finish(){
			return;
			echo "<pre>";
			var_dump($this->queries);
			echo "</pre>";
		}
	}
	
	
	
	$time = microtime(true);
	$db = new db("driver={Pervasive ODBC Client Interface};ServerName=192.168.1.103;ServerDSN=mud");
	?>

Put the following in c:\xampp\htdocs\index.php
	<?php
	include_once("../config.php");
	
	/* === BEGIN CLASS UPDATE SCRIPT === */
	function update_classes($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table classes in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE classes IN DICTIONARY USING '".$tag."clas2.dat' (
			number smallint not null, name varchar(29) not null, minhp smallint not null,
			maxhp smallint not null, exp smallint not null, nothing1 smallint not null,
			nothing2 smallint not null, nothing3 smallint not null, abila1 smallint not null,
			abila2 smallint not null, abila3 smallint not null, abila4 smallint not null,
			abila5 smallint not null, abila6 smallint not null, abila7 smallint not null,
			abila8 smallint not null, abila9 smallint not null, abila10 smallint not null,
			magictype smallint not null, magiclvl smallint not null, weapon smallint not null,
			armor smallint not null, combat smallint not null, abilb1 smallint not null,
			abilb2 smallint not null, abilb3 smallint not null, abilb4 smallint not null,
			abilb5 smallint not null, abilb6 smallint not null, abilb7 smallint not null,
			abilb8 smallint not null, abilb9 smallint not null, abilb10 smallint not null,
			nothing4 smallint not null, nothing5 smallint not null, nothing6 smallint not null,
			titletext int not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON classes (number);";
		if(!$db->dbexec($sql)) return "Error creating new index.";
	
		return "Update";
	}
	
	
	/* === BEGIN RACE UPDATE SCRIPT === */
	function update_races($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table races in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE races IN DICTIONARY USING '".$tag."race2.dat' (
			number smallint not null, name varchar(29) not null, minint smallint not null,
			minwil smallint not null, minstr smallint not null, minhea smallint not null,
			minagi smallint not null, minchm smallint not null, hpbonus smallint not null,
			nothing1 int not null, abila1 smallint not null, abila2 smallint not null,
			abila3 smallint not null, abila4 smallint not null, abila5 smallint not null,
			abila6 smallint not null, abila7 smallint not null, abila8 smallint not null,
			abila9 smallint not null, abila10 smallint not null, cp smallint not null,
			abilb1 smallint not null, abilb2 smallint not null, abilb3 smallint not null,
			abilb4 smallint not null, abilb5 smallint not null, abilb6 smallint not null,
			abilb7 smallint not null, abilb8 smallint not null, abilb9 smallint not null,
			abilb10 smallint not null, nothing2 int not null, nothing3 smallint not null,
			expchart smallint not null, nothing4 smallint not null, maxint smallint not null,
			maxwil smallint not null, maxstr smallint not null, maxhea smallint not null,
			maxagi smallint not null, maxchm smallint not null, nothing5 int not null,
			nothing6 int not null, nothing7 int not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON races (number);";
		if(!$db->dbexec($sql)) return "Error creating new index.";
		
		return "Update";	
	}
	
	/* === BEGIN SPELL UPDATE SCRIPT === */
	function update_spells($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table spells in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE spells IN DICTIONARY USING '".$tag."spel2.dat' (
			number smallint not null, name varchar(29) not null, desca varchar(50) not null,
			descb varchar(50) not null, n01 smallint not null, castmsga int not null,
			n021 smallint not null, n022 smallint not null, n023 smallint not null,
			n024 smallint not null, n025 smallint not null, n026 smallint not null,
			n027 smallint not null, n028 smallint not null, n029 smallint not null,
			n0210 smallint not null, n0211 smallint not null, lvlcap tinyint not null,
			n03 tinyint not null, msgstyle tinyint not null, n041 tinyint not null,
			n042 tinyint not null, n043 tinyint not null, abilb1 smallint not null,
			abilb2 smallint not null, abilb3 smallint not null, abilb4 smallint not null,
			abilb5 smallint not null, abilb6 smallint not null, abilb7 smallint not null,
			abilb8 smallint not null, abilb9 smallint not null, abilb10 smallint not null,
			energy smallint not null, levell smallint not null, minn smallint not null,
			maxx smallint not null, spelltype smallint not null, typeofresists smallint not null,
			difficulty smallint not null, n05 smallint not null, target smallint not null,
			duration smallint not null, typeofattack smallint not null, n06 smallint not null,
			resistability smallint not null, magerya smallint not null, abila1 smallint not null,
			abila2 smallint not null, abila3 smallint not null, abila4 smallint not null,
			abila5 smallint not null, abila6 smallint not null, abila7 smallint not null,
			abila8 smallint not null, abila9 smallint not null, abila10 smallint not null,
			castmsgb int not null, mana smallint not null, maxincrease tinyint not null,
			lvlsmaxincrease tinyint not null, mageryb smallint not null, minincrease tinyint not null,
			lvlsminincrease tinyint not null, durincrease tinyint not null, lvlsdurincrease tinyint not null,
			shortname varchar(5) not null, n07 int not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON spells (number);";
		if(!$db->dbexec($sql)) return "Error creating new index.";
		
		return "Update";
	}
	
	/* === BEGIN MONSTER UPDATE SCRIPT === */
	function update_monsters($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table monsters in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE monsters IN DICTIONARY USING '".$tag."knms2.dat' (
			Number int not null, EmptySpace varchar(49) not null, Name varchar(28) not null,
			nothing1 tinyint not null, Groupp smallint not null, nothingXX1 smallint not null,
			ExpMulti int not null, Indexx smallint not null, nothingXX3 smallint not null,
			Something2 int not null, WeaponNumber int not null, DR smallint not null,
			AC smallint not null, Something3 smallint not null, Follow smallint not null,
			MR smallint not null, BSDefence smallint not null, Experience int not null,
			Hitpoints smallint not null, Energy smallint not null, HPRegen smallint not null,
			AbilityA1 smallint not null, AbilityA2 smallint not null, AbilityA3 smallint not null,
			AbilityA4 smallint not null, AbilityA5 smallint not null, AbilityA6 smallint not null,
			AbilityA7 smallint not null, AbilityA8 smallint not null, AbilityA9 smallint not null,
			AbilityA10 smallint not null, AbilityB1 smallint not null, AbilityB2 smallint not null,
			AbilityB3 smallint not null, AbilityB4 smallint not null, AbilityB5 smallint not null,
			AbilityB6 smallint not null, AbilityB7 smallint not null, AbilityB8 smallint not null,
			AbilityB9 smallint not null, AbilityB10 smallint not null, GameLimit smallint not null,
			Active smallint not null, Type smallint not null, nothing2 tinyint not null,
			Undead tinyint not null, Alignment smallint not null, nothing3 smallint not null,
			RegenTime smallint not null, DateKilled smallint not null, TimeKilled smallint not null,
			MoveMsg int not null, DeathMsg int not null, ItemNumber1 int not null,
			ItemNumber2 int not null, ItemNumber3 int not null, ItemNumber4 int not null,
			ItemNumber5 int not null, ItemNumber6 int not null, ItemNumber7 int not null,
			ItemNumber8 int not null, ItemNumber9 int not null, ItemNumber10 int not null,
			ItemUses1 smallint not null, ItemUses2 smallint not null, ItemUses3 smallint not null,
			ItemUses4 smallint not null, ItemUses5 smallint not null, ItemUses6 smallint not null,
			ItemUses7 smallint not null, ItemUses8 smallint not null, ItemUses9 smallint not null,
			ItemUses10 smallint not null, ItemDropPer1 tinyint not null, ItemDropPer2 tinyint not null,
			ItemDropPer3 tinyint not null, ItemDropPer4 tinyint not null, ItemDropPer5 tinyint not null,
			ItemDropPer6 tinyint not null, ItemDropPer7 tinyint not null, ItemDropPer8 tinyint not null,
			ItemDropPer9 tinyint not null, ItemDropPer10 tinyint not null, nothing9 smallint not null,
			Runic int not null, Platinum int not null, Gold int not null,
			Silver int not null, Copper int not null, GreetTxt int not null,
			CharmLvL smallint not null, Nothing16 smallint not null, DescTxt int not null,
			AttackType1 tinyint not null, AttackType2 tinyint not null, AttackType3 tinyint not null,
			AttackType4 tinyint not null, AttackType5 tinyint not null, Nothing22 tinyint not null,
			AttackAccuSpell1 smallint not null, AttackAccuSpell2 smallint not null, AttackAccuSpell3 smallint not null,
			AttackAccuSpell4 smallint not null, AttackAccuSpell5 smallint not null, AttackPer1 tinyint not null,
			AttackPer2 tinyint not null, AttackPer3 tinyint not null, AttackPer4 tinyint not null,
			AttackPer5 tinyint not null, Nothing17 tinyint not null, AttackMinHCastPer1 smallint not null,
			AttackMinHCastPer2 smallint not null, AttackMinHCastPer3 smallint not null, AttackMinHCastPer4 smallint not null,
			AttackMinHCastPer5 smallint not null, AttackMaxHCastLvl1 smallint not null, AttackMaxHCastLvl2 smallint not null,
			AttackMaxHCastLvl3 smallint not null, AttackMaxHCastLvl4 smallint not null, AttackMaxHCastLvl5 smallint not null,
			Nothing18 smallint not null, AttackHitMsg1 int not null, AttackHitMsg2 int not null,
			AttackHitMsg3 int not null, AttackHitMsg4 int not null, AttackHitMsg5 int not null,
			AttackDodgeMsg1 int not null, AttackDodgeMsg2 int not null, AttackDodgeMsg3 int not null,
			AttackDodgeMsg4 int not null, AttackDodgeMsg5 int not null, AttackMissMsg1 int not null,
			AttackMissMsg2 int not null, AttackMissMsg3 int not null, AttackMissMsg4 int not null,
			AttackMissMsg5 int not null, AttackEnergy1 smallint not null, AttackEnergy2 smallint not null,
			AttackEnergy3 smallint not null, AttackEnergy4 smallint not null, AttackEnergy5 smallint not null,
			Nothing19 smallint not null, TalkTxt int not null, CharmRes smallint not null,
			Nothing21 smallint not null, AttackHitSpell1 smallint not null, AttackHitSpell2 smallint not null,
			AttackHitSpell3 smallint not null, AttackHitSpell4 smallint not null, AttackHitSpell5 smallint not null,
			DeathSpellNumber smallint not null, Nothing23 smallint not null, Nothing24 smallint not null,
			Nothing25 smallint not null, Nothing26 smallint not null, Nothing27 smallint not null,
			Nothing28 smallint not null, Nothing29 smallint not null, CreateSpellNumber smallint not null,
			SpellNumber1 smallint not null, SpellNumber2 smallint not null, SpellNumber3 smallint not null,
			SpellNumber4 smallint not null, SpellNumber5 smallint not null, SpellCastPer1 tinyint not null,
			SpellCastPer2 tinyint not null, SpellCastPer3 tinyint not null, SpellCastPer4 tinyint not null,
			SpellCastPer5 tinyint not null, SpellCastLvl1 tinyint not null, SpellCastLvl2 tinyint not null,
			SpellCastLvl3 tinyint not null, SpellCastLvl4 tinyint not null, SpellCastLvl5 tinyint not null,
			DescLine1 varchar(70) not null, DescLine2 varchar(70) not null, DescLine3 varchar(70) not null,
			DescLine4 varchar(70) not null, Gender tinyint not null, Nothing14 tinyint not null,
			Nothing15 smallint not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON monsters (number);";
		if(!$db->dbexec($sql)) return "Error creating new index.";
		
		return "Update";
	}
	
	/* === BEGIN ITEM UPDATE SCRIPT === */
	function update_items($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table items in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE items IN DICTIONARY USING '".$tag."item2.dat' (
			Number int not null, unknown1 smallint not null, GameLimit smallint not null,
			unknown2 smallint not null, unknown3 smallint not null, unknown4 smallint not null,
			unknown5 smallint not null, EmptySpace1 varchar(156) not null, Name varchar(29) not null,
			Desc1 varchar(60) not null, Desc2 varchar(60) not null, Desc3 varchar(60) not null,
			Desc4 varchar(60) not null, Desc5 varchar(60) not null, Desc6 varchar(60) not null,
			Desc7 varchar(60) not null, Desc8 varchar(60) not null, Desc9 varchar(60) not null,
			unknown6 smallint not null, Weight smallint not null, Type smallint not null,
			AbilityA1 smallint not null, AbilityA2 smallint not null, AbilityA3 smallint not null,
			AbilityA4 smallint not null, AbilityA5 smallint not null, AbilityA6 smallint not null,
			AbilityA7 smallint not null, AbilityA8 smallint not null, AbilityA9 smallint not null,
			AbilityA10 smallint not null, AbilityA11 smallint not null, AbilityA12 smallint not null,
			AbilityA13 smallint not null, AbilityA14 smallint not null, AbilityA15 smallint not null,
			AbilityA16 smallint not null, AbilityA17 smallint not null, AbilityA18 smallint not null,
			AbilityA19 smallint not null, AbilityA20 smallint not null, Uses smallint not null,
			unknown7 smallint not null, Cost smallint not null, Class1 smallint not null,
			Class2 smallint not null, Class3 smallint not null, Class4 smallint not null,
			Class5 smallint not null, Class6 smallint not null, Class7 smallint not null,
			Class8 smallint not null, Class9 smallint not null, Class10 smallint not null,
			unknown8 smallint not null, unknown9 smallint not null, unknown10 smallint not null,
			Minhit smallint not null, Maxhit smallint not null, AC smallint not null,
			Race1 smallint not null, Race2 smallint not null, Race3 smallint not null,
			Race4 smallint not null, Race5 smallint not null, Race6 smallint not null, 
			Race7 smallint not null, Race8 smallint not null, Race9 smallint not null,
			Race10 smallint not null, unknown111 smallint not null, unknown112 smallint not null,
			unknown113 smallint not null, unknown114 smallint not null, unknown115 smallint not null,
			unknown116 smallint not null, unknown117 smallint not null, unknown118 smallint not null,
			unknown119 smallint not null, unknown1110 smallint not null, Negate1 smallint not null,
			Negate2 smallint not null, Negate3 smallint not null, Negate4 smallint not null,
			Negate5 smallint not null, Negate6 smallint not null, Negate7 smallint not null,
			Negate8 smallint not null, Negate9 smallint not null, Negate10 smallint not null,
			Negate11 smallint not null, Negate12 smallint not null, Negate13 smallint not null,
			Negate14 smallint not null, Negate15 smallint not null, Negate16 smallint not null,
			Negate17 smallint not null, Negate18 smallint not null, Negate19 smallint not null,
			Negate20 smallint not null, Weapon smallint not null, Armour smallint not null,
			WornOn smallint not null, Accuracy smallint not null, DR smallint not null,
			Gettable tinyint not null, unknown12 tinyint not null, ReqStr smallint not null,
			unknown13a1 smallint not null, unknown13a2 smallint not null, unknown13a3 smallint not null,
			unknown13a4 smallint not null, unknown13a5 smallint not null, OpenRunic int not null,
			OpenPlatinum int not null, OpenGold int not null, OpenSilver int not null,
			OpenCopper int not null, unknown13b1 smallint not null, unknown13b2 smallint not null,
			unknown13b3 smallint not null, unknown13b4 smallint not null, unknown13b5 smallint not null,
			unknown13b6 smallint not null, unknown13b7 smallint not null, unknown13b8 smallint not null,
			unknown13b9 smallint not null, unknown13b10 smallint not null, unknown13b11 smallint not null,
			unknown13b12 smallint not null, unknown13b13 smallint not null, unknown13b14 smallint not null, 
			unknown13b15 smallint not null, Speed smallint not null, unknown14 smallint not null,
			AbilityB1 smallint not null, AbilityB2 smallint not null, AbilityB3 smallint not null,
			AbilityB4 smallint not null, AbilityB5 smallint not null, AbilityB6 smallint not null,
			AbilityB7 smallint not null, AbilityB8 smallint not null, AbilityB9 smallint not null,
			AbilityB10 smallint not null, AbilityB11 smallint not null, AbilityB12 smallint not null,
			AbilityB13 smallint not null, AbilityB14 smallint not null, AbilityB15 smallint not null,
			AbilityB16 smallint not null, AbilityB17 smallint not null, AbilityB18 smallint not null,
			AbilityB19 smallint not null, AbilityB20 smallint not null, unknown15 smallint not null,
			HitMsg int not null, MissMsg int not null, ReadTB int not null,
			DistructMsg int not null, unknown161 smallint not null, unknown162 smallint not null,
			unknown163 smallint not null, unknown164 smallint not null, unknown165 smallint not null,
			unknown166 smallint not null, NotDroppable tinyint not null, CostType tinyint not null,
			RetainAfterUses tinyint not null, Robable tinyint not null, DestroyOnDeath tinyint not null,
			unknown19 tinyint not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON items (number);";
		if(!$db->dbexec($sql)) return "Error creating new index.";
		
		return "Update";
	}
	
	/* === BEGIN SHOP UPDATE SCRIPT === */
	function update_shops($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table shops in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE shops IN DICTIONARY USING '".$tag."shop2.dat' (
			Number int not null, Name varchar(38) not null, ShopAfterName smallint not null,
			ShopDescriptionA varchar(52) not null, ShopDescriptionB varchar(52) not null, ShopDescriptionC varchar(52) not null,
			ShopType smallint not null, ShopMinLvL smallint not null, ShopMaxLvl smallint not null,
			ShopMarkUp smallint not null, ShopNothing4 smallint not null, ShopClassLimit tinyint not null,
			ShopNothingAA tinyint not null, ShopItemNumber1 int not null, ShopItemNumber2 int not null,
			ShopItemNumber3 int not null, ShopItemNumber4 int not null, ShopItemNumber5 int not null,
			ShopItemNumber6 int not null, ShopItemNumber7 int not null, ShopItemNumber8 int not null,
			ShopItemNumber9 int not null, ShopItemNumber10 int not null, ShopItemNumber11 int not null,
			ShopItemNumber12 int not null, ShopItemNumber13 int not null, ShopItemNumber14 int not null,
			ShopItemNumber15 int not null, ShopItemNumber16 int not null, ShopItemNumber17 int not null,
			ShopItemNumber18 int not null, ShopItemNumber19 int not null, ShopItemNumber20 int not null,
			ShopMax1 smallint not null, ShopMax2 smallint not null, ShopMax3 smallint not null,
			ShopMax4 smallint not null, ShopMax5 smallint not null, ShopMax6 smallint not null,
			ShopMax7 smallint not null, ShopMax8 smallint not null, ShopMax9 smallint not null,
			ShopMax10 smallint not null, ShopMax11 smallint not null, ShopMax12 smallint not null,
			ShopMax13 smallint not null, ShopMax14 smallint not null, ShopMax15 smallint not null,
			ShopMax16 smallint not null, ShopMax17 smallint not null, ShopMax18 smallint not null,
			ShopMax19 smallint not null, ShopMax20 smallint not null, ShopNow1 smallint not null,
			ShopNow2 smallint not null, ShopNow3 smallint not null, ShopNow4 smallint not null,
			ShopNow5 smallint not null, ShopNow6 smallint not null, ShopNow7 smallint not null,
			ShopNow8 smallint not null, ShopNow9 smallint not null, ShopNow10 smallint not null,
			ShopNow11 smallint not null, ShopNow12 smallint not null, ShopNow13 smallint not null,
			ShopNow14 smallint not null, ShopNow15 smallint not null, ShopNow16 smallint not null,
			ShopNow17 smallint not null, ShopNow18 smallint not null, ShopNow19 smallint not null,
			ShopNow20 smallint not null, ShopRgnTime1 smallint not null, ShopRgnTime2 smallint not null,
			ShopRgnTime3 smallint not null, ShopRgnTime4 smallint not null, ShopRgnTime5 smallint not null,
			ShopRgnTime6 smallint not null, ShopRgnTime7 smallint not null, ShopRgnTime8 smallint not null,
			ShopRgnTime9 smallint not null, ShopRgnTime10 smallint not null, ShopRgnTime11 smallint not null,
			ShopRgnTime12 smallint not null, ShopRgnTime13 smallint not null, ShopRgnTime14 smallint not null,
			ShopRgnTime15 smallint not null, ShopRgnTime16 smallint not null, ShopRgnTime17 smallint not null,
			ShopRgnTime18 smallint not null, ShopRgnTime19 smallint not null, ShopRgnTime20 smallint not null,
			ShopRgnNumber1 smallint not null, ShopRgnNumber2 smallint not null, ShopRgnNumber3 smallint not null,
			ShopRgnNumber4 smallint not null, ShopRgnNumber5 smallint not null, ShopRgnNumber6 smallint not null,
			ShopRgnNumber7 smallint not null, ShopRgnNumber8 smallint not null, ShopRgnNumber9 smallint not null,
			ShopRgnNumber10 smallint not null, ShopRgnNumber11 smallint not null, ShopRgnNumber12 smallint not null,
			ShopRgnNumber13 smallint not null, ShopRgnNumber14 smallint not null, ShopRgnNumber15 smallint not null,
			ShopRgnNumber16 smallint not null, ShopRgnNumber17 smallint not null, ShopRgnNumber18 smallint not null,
			ShopRgnNumber19 smallint not null, ShopRgnNumber20 smallint not null, ShopRgnPercentage1 tinyint not null,
			ShopRgnPercentage2 tinyint not null, ShopRgnPercentage3 tinyint not null, ShopRgnPercentage4 tinyint not null,
			ShopRgnPercentage5 tinyint not null, ShopRgnPercentage6 tinyint not null, ShopRgnPercentage7 tinyint not null,
			ShopRgnPercentage8 tinyint not null, ShopRgnPercentage9 tinyint not null, ShopRgnPercentage10 tinyint not null,
			ShopRgnPercentage11 tinyint not null, ShopRgnPercentage12 tinyint not null, ShopRgnPercentage13 tinyint not null,
			ShopRgnPercentage14 tinyint not null, ShopRgnPercentage15 tinyint not null, ShopRgnPercentage16 tinyint not null,
			ShopRgnPercentage17 tinyint not null, ShopRgnPercentage18 tinyint not null, ShopRgnPercentage19 tinyint not null,
			ShopRgnPercentage20 tinyint not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON shops (number);";
		if(!$db->dbexec($sql)) return "Error creating new index.";
	
		return "Update";
	}
	
	/* === BEGIN ROOM UPDATE SCRIPT === */
	function update_rooms($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table rooms in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE rooms IN DICTIONARY USING '".$tag."mp002.dat' (
			MapNumber int not null, RoomNumber int not null, EmptySpace varchar(252) not null,
			Name varchar(52) not null, Desc1 varchar(70) not null, Desc2 varchar(70) not null,
			Desc3 varchar(70) not null, Desc4 varchar(70) not null, Desc5 varchar(70) not null,
			Desc6 varchar(70) not null, Desc7 varchar(70) not null, AnsiMap varchar(12) not null,
			RoomExit1 int not null, RoomExit2 int not null, RoomExit3 int not null,
			RoomExit4 int not null, RoomExit5 int not null, RoomExit6 int not null,
			RoomExit7 int not null, RoomExit8 int not null, RoomExit9 int not null,
			RoomExit10 int not null, RoomType1 smallint not null, RoomType2 smallint not null, 
			RoomType3 smallint not null, RoomType4 smallint not null, RoomType5 smallint not null,
			RoomType6 smallint not null, RoomType7 smallint not null, RoomType8 smallint not null,
			RoomType9 smallint not null, RoomType10 smallint not null, Para11 int not null,
			Para12 int not null, Para13 int not null, Para14 int not null,
			Para15 int not null, Para16 int not null, Para17 int not null,
			Para18 int not null, Para19 int not null, Para110 int not null,
			Para21 smallint not null, Para22 smallint not null, Para23 smallint not null,
			Para24 smallint not null, Para25 smallint not null, Para26 smallint not null,
			Para27 smallint not null, Para28 smallint not null, Para29 smallint not null,
			Para210 smallint not null, Para31 int not null, Para32 int not null,
			Para33 int not null, Para34 int not null, Para35 int not null,  
			Para36 int not null, Para37 int not null, Para38 int not null,
			Para39 int not null, Para310 int not null, Para41 int not null,
			Para42 int not null, Para43 int not null, Para44 int not null,
			Para45 int not null, Para46 int not null, Para47 int not null,
			Para48 int not null, Para49 int not null, Para410 int not null,
			CurrentRoomMon1 int not null, CurrentRoomMon2 int not null, CurrentRoomMon3 int not null,
			CurrentRoomMon4 int not null, CurrentRoomMon5 int not null, CurrentRoomMon6 int not null,
			CurrentRoomMon7 int not null, CurrentRoomMon8 int not null, CurrentRoomMon9 int not null,
			CurrentRoomMon10 int not null, CurrentRoomMon11 int not null, CurrentRoomMon12 int not null,
			CurrentRoomMon13 int not null, CurrentRoomMon14 int not null, CurrentRoomMon15 int not null,
			Type smallint not null, NewSpot smallint not null, ShopNum int not null,
			nothing1111 smallint not null, nothing12 smallint not null, nothing13 smallint not null,
			nothing14 smallint not null, nothing15 smallint not null, nothing16 smallint not null,
			nothing17 smallint not null, nothing18 smallint not null, nothing19 smallint not null,
			nothing110 smallint not null, nothing111 smallint not null, nothing112 smallint not null,
			nothing113 smallint not null, nothing114 smallint not null, nothing115 smallint not null,
			MinIndex smallint not null, MaxIndex smallint not null, ByNumber int not null,
			dontknow smallint not null, Light smallint not null, GangHouseNumber smallint not null,
			RoomItems1 int not null, RoomItems2 int not null, RoomItems3 int not null,
			RoomItems4 int not null, RoomItems5 int not null, RoomItems6 int not null,
			RoomItems7 int not null, RoomItems8 int not null, RoomItems9 int not null,
			RoomItems10 int not null, RoomItems11 int not null, RoomItems12 int not null,
			RoomItems13 int not null, RoomItems14 int not null, RoomItems15 int not null,
			RoomItems16 int not null, RoomItems17 int not null, RoomItemUses1 smallint not null,
			RoomItemUses2 smallint not null, RoomItemUses3 smallint not null, RoomItemUses4 smallint not null,
			RoomItemUses5 smallint not null, RoomItemUses6 smallint not null, RoomItemUses7 smallint not null,
			RoomItemUses8 smallint not null, RoomItemUses9 smallint not null, RoomItemUses10 smallint not null,
			RoomItemUses11 smallint not null, RoomItemUses12 smallint not null, RoomItemUses13 smallint not null,
			RoomItemUses14 smallint not null, RoomItemUses15 smallint not null, RoomItemUses16 smallint not null,
			RoomItemUses17 smallint not null, nothing4 smallint not null, InvisItems1 int not null,
			InvisItems2 int not null, InvisItems3 int not null, InvisItems4 int not null,
			InvisItems5 int not null, InvisItems6 int not null, InvisItems7 int not null,
			InvisItems8 int not null, InvisItems9 int not null, InvisItems10 int not null,
			InvisItems11 int not null, InvisItems12 int not null, InvisItems13 int not null,
			InvisItems14 int not null, InvisItems15 int not null, InvisItemUses1 smallint not null,
			InvisItemUses2 smallint not null, InvisItemUses3 smallint not null, InvisItemUses4 smallint not null,
			InvisItemUses5 smallint not null, InvisItemUses6 smallint not null, InvisItemUses7 smallint not null,
			InvisItemUses8 smallint not null, InvisItemUses9 smallint not null, InvisItemUses10 smallint not null,
			InvisItemUses11 smallint not null, InvisItemUses12 smallint not null, InvisItemUses13 smallint not null,
			InvisItemUses14 smallint not null, InvisItemUses15 smallint not null, nothing5 smallint not null,
			Runic int not null, Platinum int not null, Gold int not null,
			Silver int not null, Copper int not null, nothing71 int not null,
			nothing72 int not null, nothing73 int not null, nothing74 int not null,
			nothing75 int not null, MaxRegen int not null, MonsterType smallint not null,
			unknown69 smallint not null, Attributes int not null, nothing9 int not null,
			DeathRoom int not null, ExitRoom int not null, RoomItemQty1 smallint not null,
			RoomItemQty2 smallint not null, RoomItemQty3 smallint not null, RoomItemQty4 smallint not null,
			RoomItemQty5 smallint not null, RoomItemQty6 smallint not null, RoomItemQty7 smallint not null,
			RoomItemQty8 smallint not null, RoomItemQty9 smallint not null, RoomItemQty10 smallint not null,
			RoomItemQty11 smallint not null, RoomItemQty12 smallint not null, RoomItemQty13 smallint not null,
			RoomItemQty14 smallint not null, RoomItemQty15 smallint not null, RoomItemQty16 smallint not null,
			RoomItemQty17 smallint not null, InvisItemQty1 smallint not null, InvisItemQty2 smallint not null,
			InvisItemQty3 smallint not null, InvisItemQty4 smallint not null, InvisItemQty5 smallint not null,
			InvisItemQty6 smallint not null, InvisItemQty7 smallint not null, InvisItemQty8 smallint not null,
			InvisItemQty9 smallint not null, InvisItemQty10 smallint not null, InvisItemQty11 smallint not null,
			InvisItemQty12 smallint not null, InvisItemQty13 smallint not null, InvisItemQty14 smallint not null,
			InvisItemQty15 smallint not null, CmdText int not null, nothing10 int not null,
			Delay smallint not null, MaxArea smallint not null, Nothing11 int not null,
			ControlRoom int not null, PermNPC int not null, PlacedItems1 int not null,
			PlacedItems2 int not null, PlacedItems3 int not null, PlacedItems4 int not null,
			PlacedItems5 int not null, PlacedItems6 int not null, PlacedItems7 int not null,
			PlacedItems8 int not null, PlacedItems9 int not null, PlacedItems10 int not null,
			Nothing121 int not null, Nothing122 int not null, Something1 int not null,
			Spell int not null, unknown70 smallint not null, NumMons tinyint not null,
			unknown71 tinyint not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		//$sql = "CREATE NOT MODIFIABLE INDEX key0 ON rooms (MapNumber);";
		//if(!$db->dbexec($sql)) return "Error creating new index.";
	
		return "Update";
	}
	
	/* === BEGIN MESSAGE UPDATE SCRIPT === */
	function update_messages($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table messages in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE messages IN DICTIONARY USING '".$tag."msg2.dat' (
			Number int not null, MsgLine1 varchar(81) not null,
			MsgLine2 varchar(81) not null, MsgLine3 varchar(81) not null, Offset integer not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON messages (number);";
		if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN TEXTBLOCK UPDATE SCRIPT === */
	function update_textblocks($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table textblocks in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE textblocks IN DICTIONARY USING '".$tag."text2.dat' (
			PartNum smallint not null, LeadIn1 tinyint not null, LeadIn2 tinyint not null,
			LeadIn3 tinyint not null, LeadIn4 tinyint not null, LeadIn5 tinyint not null,
			LeadIn6 tinyint not null, LeadIn7 tinyint not null, LeadIn8 tinyint not null,
			LeadIn9 tinyint not null, LeadIn10 tinyint not null, LeadIn11 tinyint not null,
			LeadIn12 tinyint not null, LeadIn13 tinyint not null, LeadIn14 tinyint not null,
			Number int not null, LinkTo int not null, Data varchar(1999) not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		//$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON messages (number);";
		//if(!$db->dbexec($sql)) return "Error creating new index.";
	
		return "Update";
	}
	
	function update_users($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table users in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		echo "<br />Creating New User Tables... ";
		$sql = "CREATE TABLE users IN DICTIONARY USING '".$tag."user2.dat' (
		BBSName varchar(29) not null, FirstName varchar(10) not null, LastName varchar(18) not null,
		NotExperience int not null, SpellCasted1 smallint not null, SpellCasted2 smallint not null,
		SpellCasted3 smallint not null, SpellCasted4 smallint not null, SpellCasted5 smallint not null,
		SpellCasted6 smallint not null, SpellCasted7 smallint not null, SpellCasted8 smallint not null,
		SpellCasted9 smallint not null, SpellCasted10 smallint not null, SpellValue1 smallint not null,
		SpellValue2 smallint not null, SpellValue3 smallint not null, SpellValue4 smallint not null,
		SpellValue5 smallint not null, SpellValue6 smallint not null, SpellValue7 smallint not null,
		SpellValue8 smallint not null, SpellValue9 smallint not null, SpellValue10 smallint not null,
		SpellRoundsLeft1 smallint not null, SpellRoundsLeft2 smallint not null, SpellRoundsLeft3 smallint not null,
		SpellRoundsLeft4 smallint not null, SpellRoundsLeft5 smallint not null, SpellRoundsLeft6 smallint not null,
		SpellRoundsLeft7 smallint not null, SpellRoundsLeft8 smallint not null, SpellRoundsLeft9 smallint not null,
		SpellRoundsLeft10 smallint not null, Title varchar(19) not null, Race smallint not null,
		Class smallint not null, Level smallint not null, Stat1 smallint not null,
		Stat2 smallint not null, Stat3 smallint not null, Stat4 smallint not null,
		Stat5 smallint not null, Stat6 smallint not null, Stat7 smallint not null,
		Stat8 smallint not null, Stat9 smallint not null, Stat10 smallint not null,
		Stat11 smallint not null, Stat12 smallint not null, MaxHP smallint not null,
		CurrentHP smallint not null, MaxENC smallint not null, CurrentENC smallint not null,
		Energy1 smallint not null, Energy2 smallint not null, Energy3 smallint not null,
		unknown11 smallint not null, unknown12 smallint not null, MagicRes smallint not null,
		MagicRes2 smallint not null, MapNumber int not null, RoomNum int not null,
		nothing2 smallint not null, unknown21 smallint not null, unknown22 smallint not null,
		nothing3 smallint not null, unknown31 tinyint not null, unknown32 tinyint not null,
		nothing4 smallint not null, Item1 int not null, Item2 int not null,
		Item3 int not null, Item4 int not null, Item5 int not null,
		Item6 int not null, Item7 int not null, Item8 int not null,
		Item9 int not null, Item10 int not null, Item11 int not null,
		Item12 int not null, Item13 int not null, Item14 int not null,
		Item15 int not null, Item16 int not null, Item17 int not null,
		Item18 int not null, Item19 int not null, Item20 int not null,
		Item21 int not null, Item22 int not null, Item23 int not null,
		Item24 int not null, Item25 int not null, Item26 int not null,
		Item27 int not null, Item28 int not null, Item29 int not null,
		Item30 int not null, Item31 int not null, Item32 int not null,
		Item33 int not null, Item34 int not null, Item35 int not null,
		Item36 int not null, Item37 int not null, Item38 int not null,
		Item39 int not null, Item40 int not null, Item41 int not null,
		Item42 int not null, Item43 int not null, Item44 int not null,
		Item45 int not null, Item46 int not null, Item47 int not null,
		Item48 int not null, Item49 int not null, Item50 int not null,
		Item51 int not null, Item52 int not null, Item53 int not null,
		Item54 int not null, Item55 int not null, Item56 int not null,
		Item57 int not null, Item58 int not null, Item59 int not null, 
		Item60 int not null, Item61 int not null, Item62 int not null,
		Item63 int not null, Item64 int not null, Item65 int not null,
		Item66 int not null, Item67 int not null, Item68 int not null,
		Item69 int not null, Item70 int not null, Item71 int not null,
		Item72 int not null, Item73 int not null, Item74 int not null,
		Item75 int not null, Item76 int not null, Item77 int not null,
		Item78 int not null, Item79 int not null, Item80 int not null,
		Item81 int not null, Item82 int not null, Item83 int not null,
		Item84 int not null, Item85 int not null, Item86 int not null,
		Item87 int not null, Item88 int not null, Item89 int not null,
		Item90 int not null, Item91 int not null, Item92 int not null,
		Item93 int not null, Item94 int not null, Item95 int not null,
		Item96 int not null, Item97 int not null, Item98 int not null,
		Item99 int not null, Item100 int not null, ItemUses1 smallint not null,
		ItemUses2 smallint not null, ItemUses3 smallint not null, ItemUses4 smallint not null, 
		ItemUses5 smallint not null, ItemUses6 smallint not null, ItemUses7 smallint not null,
		ItemUses8 smallint not null, ItemUses9 smallint not null, ItemUses10 smallint not null,
		ItemUses11 smallint not null, ItemUses12 smallint not null, ItemUses13 smallint not null,
		ItemUses14 smallint not null, ItemUses15 smallint not null, ItemUses16 smallint not null,
		ItemUses17 smallint not null, ItemUses18 smallint not null, ItemUses19 smallint not null,
		ItemUses20 smallint not null, ItemUses21 smallint not null, ItemUses22 smallint not null,
		ItemUses23 smallint not null, ItemUses24 smallint not null, ItemUses25 smallint not null,
		ItemUses26 smallint not null, ItemUses27 smallint not null, ItemUses28 smallint not null,
		ItemUses29 smallint not null, ItemUses30 smallint not null, ItemUses31 smallint not null,
		ItemUses32 smallint not null, ItemUses33 smallint not null, ItemUses34 smallint not null,
		ItemUses35 smallint not null, ItemUses36 smallint not null, ItemUses37 smallint not null,
		ItemUses38 smallint not null, ItemUses39 smallint not null, ItemUses40 smallint not null,
		ItemUses41 smallint not null, ItemUses42 smallint not null, ItemUses43 smallint not null,
		ItemUses44 smallint not null, ItemUses45 smallint not null, ItemUses46 smallint not null,
		ItemUses47 smallint not null, ItemUses48 smallint not null, ItemUses49 smallint not null,
		ItemUses50 smallint not null, ItemUses51 smallint not null, ItemUses52 smallint not null,
		ItemUses53 smallint not null, ItemUses54 smallint not null, ItemUses55 smallint not null,
		ItemUses56 smallint not null, ItemUses57 smallint not null, ItemUses58 smallint not null,
		ItemUses59 smallint not null, ItemUses60 smallint not null, ItemUses61 smallint not null,
		ItemUses62 smallint not null, ItemUses63 smallint not null, ItemUses64 smallint not null,
		ItemUses65 smallint not null, ItemUses66 smallint not null, ItemUses67 smallint not null,
		ItemUses68 smallint not null, ItemUses69 smallint not null, ItemUses70 smallint not null,
		ItemUses71 smallint not null, ItemUses72 smallint not null, ItemUses73 smallint not null,
		ItemUses74 smallint not null, ItemUses75 smallint not null, ItemUses76 smallint not null,
		ItemUses77 smallint not null, ItemUses78 smallint not null, ItemUses79 smallint not null,
		ItemUses80 smallint not null, ItemUses81 smallint not null, ItemUses82 smallint not null,
		ItemUses83 smallint not null, ItemUses84 smallint not null, ItemUses85 smallint not null,
		ItemUses86 smallint not null, ItemUses87 smallint not null, ItemUses88 smallint not null,
		ItemUses89 smallint not null, ItemUses90 smallint not null, ItemUses91 smallint not null,
		ItemUses92 smallint not null, ItemUses93 smallint not null, ItemUses94 smallint not null,
		ItemUses95 smallint not null, ItemUses96 smallint not null, ItemUses97 smallint not null,
		ItemUses98 smallint not null, ItemUses99 smallint not null, ItemUses100 smallint not null,
		nothing5 int not null, Key1 int not null, Key2 int not null, Key3 int not null,
		Key4 int not null, Key5 int not null, Key6 int not null, Key7 int not null,
		Key8 int not null, Key9 int not null, Key10 int not null, Key11 int not null,
		Key12 int not null, Key13 int not null, Key14 int not null, Key15 int not null,
		Key16 int not null, Key17 int not null, Key18 int not null, Key19 int not null,
		Key20 int not null, Key21 int not null, Key22 int not null, Key23 int not null,
		Key24 int not null, Key25 int not null, Key26 int not null, Key27 int not null,
		Key28 int not null, Key29 int not null, Key30 int not null, Key31 int not null,
		Key32 int not null, Key33 int not null, Key34 int not null, Key35 int not null,
		Key36 int not null, Key37 int not null, Key38 int not null, Key39 int not null,
		Key40 int not null, Key41 int not null, Key42 int not null, Key43 int not null,
		Key44 int not null, Key45 int not null, Key46 int not null, Key47 int not null,
		Key48 int not null, Key49 int not null, Key50 int not null,	KeyUses1 smallint not null,
		KeyUses2 smallint not null, KeyUses3 smallint not null,	KeyUses4 smallint not null,
		KeyUses5 smallint not null,	KeyUses6 smallint not null,	KeyUses7 smallint not null,
		KeyUses8 smallint not null, KeyUses9 smallint not null,	KeyUses10 smallint not null,
		KeyUses11 smallint not null, KeyUses12 smallint not null, KeyUses13 smallint not null,
		KeyUses14 smallint not null, KeyUses15 smallint not null, KeyUses16 smallint not null,
		KeyUses17 smallint not null, KeyUses18 smallint not null, KeyUses19 smallint not null,
		KeyUses20 smallint not null, KeyUses21 smallint not null, KeyUses22 smallint not null,
		KeyUses23 smallint not null, KeyUses24 smallint not null, KeyUses25 smallint not null,
		KeyUses26 smallint not null, KeyUses27 smallint not null, KeyUses28 smallint not null,
		KeyUses29 smallint not null, KeyUses30 smallint not null, KeyUses31 smallint not null,
		KeyUses32 smallint not null, KeyUses33 smallint not null, KeyUses34 smallint not null,
		KeyUses35 smallint not null, KeyUses36 smallint not null, KeyUses37 smallint not null,
		KeyUses38 smallint not null, KeyUses39 smallint not null, KeyUses40 smallint not null,
		KeyUses41 smallint not null, KeyUses42 smallint not null, KeyUses43 smallint not null,
		KeyUses44 smallint not null, KeyUses45 smallint not null, KeyUses46 smallint not null,
		KeyUses47 smallint not null, KeyUses48 smallint not null, KeyUses49 smallint not null,
		KeyUses50 smallint not null, unknown41 int not null, unknown42 int not null,
		unknown43 int not null, unknown44 int not null, BillionsOfExperience int not null,
		MillionsOfExperience int not null, Nothing6 smallint not null, Spell1 smallint not null,
		Spell2 smallint not null, Spell3 smallint not null, Spell4 smallint not null,
		Spell5 smallint not null, Spell6 smallint not null, Spell7 smallint not null,
		Spell8 smallint not null, Spell9 smallint not null, Spell10 smallint not null,
		Spell11 smallint not null, Spell12 smallint not null, Spell13 smallint not null,
		Spell14 smallint not null, Spell15 smallint not null, Spell16 smallint not null,
		Spell17 smallint not null, Spell18 smallint not null, Spell19 smallint not null,
		Spell20 smallint not null, Spell21 smallint not null, Spell22 smallint not null,
		Spell23 smallint not null, Spell24 smallint not null, Spell25 smallint not null,
		Spell26 smallint not null, Spell27 smallint not null, Spell28 smallint not null,
		Spell29 smallint not null, Spell30 smallint not null, Spell31 smallint not null,
		Spell32 smallint not null, Spell33 smallint not null, Spell34 smallint not null,
		Spell35 smallint not null, Spell36 smallint not null, Spell37 smallint not null,
		Spell38 smallint not null, Spell39 smallint not null, Spell40 smallint not null,
		Spell41 smallint not null, Spell42 smallint not null, Spell43 smallint not null,
		Spell44 smallint not null, Spell45 smallint not null, Spell46 smallint not null,
		Spell47 smallint not null, Spell48 smallint not null, Spell49 smallint not null,
		Spell50 smallint not null, Spell51 smallint not null, Spell52 smallint not null,
		Spell53 smallint not null, Spell54 smallint not null, Spell55 smallint not null,
		Spell56 smallint not null, Spell57 smallint not null, Spell58 smallint not null,
		Spell59 smallint not null, Spell60 smallint not null, Spell61 smallint not null,
		Spell62 smallint not null, Spell63 smallint not null, Spell64 smallint not null,
		Spell65 smallint not null, Spell66 smallint not null, Spell67 smallint not null,
		Spell68 smallint not null, Spell69 smallint not null, Spell70 smallint not null,
		Spell71 smallint not null, Spell72 smallint not null, Spell73 smallint not null,
		Spell74 smallint not null, Spell75 smallint not null, Spell76 smallint not null,
		Spell77 smallint not null, Spell78 smallint not null, Spell79 smallint not null,
		Spell80 smallint not null, Spell81 smallint not null, Spell82 smallint not null,
		Spell83 smallint not null, Spell84 smallint not null, Spell85 smallint not null,
		Spell86 smallint not null, Spell87 smallint not null, Spell88 smallint not null,
		Spell89 smallint not null, Spell90 smallint not null, Spell91 smallint not null,
		Spell92 smallint not null, Spell93 smallint not null, Spell94 smallint not null,
		Spell95 smallint not null, Spell96 smallint not null, Spell97 smallint not null,
		Spell98 smallint not null, Spell99 smallint not null, Spell100 smallint not null,
		EvilPoints smallint not null, nothing71 int not null, nothing72 int not null,
		nothing73 int not null,	LastMap1 int not null, LastMap2 int not null,
		LastMap3 int not null, LastMap4 int not null, LastMap5 int not null,
		LastMap6 int not null, LastMap7 int not null, LastMap8 int not null,
		LastMap9 int not null, LastMap10 int not null, LastMap11 int not null,
		LastMap12 int not null, LastMap13 int not null, LastMap14 int not null,
		LastMap15 int not null, LastMap16 int not null, LastMap17 int not null,
		LastMap18 int not null, LastMap19 int not null, LastMap20 int not null,
		LastRoom1 int not null, LastRoom2 int not null, LastRoom3 int not null,
		LastRoom4 int not null, LastRoom5 int not null, LastRoom6 int not null,
		LastRoom7 int not null, LastRoom8 int not null, LastRoom9 int not null,
		LastRoom10 int not null, LastRoom11 int not null, LastRoom12 int not null,
		LastRoom13 int not null, LastRoom14 int not null, LastRoom15 int not null,
		LastRoom16 int not null, LastRoom17 int not null, LastRoom18 int not null,
		LastRoom19 int not null, LastRoom20 int not null, nothing8 smallint not null,
		BroadcastChan smallint not null, unknown5 int not null, Perception smallint not null,
		Stealth smallint not null,
		MartialArts smallint not null,
		Thievery smallint not null,
		MaxMana smallint not null,
		CurrentMana smallint not null,
		SpellCasting smallint not null,
		Traps smallint not null,
		unknown6 smallint not null,
		Picklocks smallint not null,
		Tracking smallint not null,
		nothing9 smallint not null,
		Runic int not null,
		Platinum int not null,
		Gold int not null,
		Silver int not null,
		Copper int not null,
		WeaponHand int not null,
		nothing10 int not null,
		WornItem1 int not null,
		WornItem2 int not null,
		WornItem3 int not null,
		WornItem4 int not null,
		WornItem5 int not null,
		WornItem6 int not null,
		WornItem7 int not null,
		WornItem8 int not null,
		WornItem9 int not null,
		WornItem10 int not null,
		WornItem11 int not null,
		WornItem12 int not null,
		WornItem13 int not null,
		WornItem14 int not null,
		WornItem15 int not null,
		WornItem16 int not null,
		WornItem17 int not null,
		WornItem18 int not null,
		WornItem19 int not null,
		WornItem20 int not null,
		unknown71 smallint not null,
		unknown72 smallint not null,
		unknown73 smallint not null,
		unknown74 smallint not null,
		unknown75 smallint not null,
		unknown76 smallint not null,
		unknown77 smallint not null,
		unknown78 smallint not null,
		unknown79 smallint not null,
		unknown710 smallint not null,
		unknown711 smallint not null,
		unknown712 smallint not null,
		unknown713 smallint not null,
		unknown714 smallint not null,
		unknown715 smallint not null,
		unknown716 smallint not null,
		unknown717 smallint not null,
		unknown718 smallint not null,
		unknown719 smallint not null,
		unknown720 smallint not null,
		unknown8 smallint not null,
		LivesRemaining smallint not null,
		unknown91 smallint not null,
		unknown92 smallint not null,
		unknown93 smallint not null,
		unknown94 smallint not null,
		unknown95 smallint not null,
		unknown96 smallint not null,
		unknown97 smallint not null,
		unknown98 smallint not null,
		unknown99 smallint not null,
		unknown910 smallint not null,
		unknown911 smallint not null,
		unknown912 smallint not null,
		unknown913 smallint not null,
		unknown914 smallint not null,
		unknown915 smallint not null,
		unknown916 smallint not null,
		GangName varchar(19) not null,
		unknown111 tinyint not null,
		unknown112 tinyint not null,
		unknown113 tinyint not null,
		unknown114 tinyint not null,
		unknown115 tinyint not null,
		unknown116 tinyint not null,
		CPRemaining smallint not null,
		SuicidePassword varchar(7) not null,
		unknown12a1 smallint not null,
		unknown12a2 smallint not null,
		unknown12a3 smallint not null,
		unknown12a4 smallint not null,
		unknown12a5 smallint not null,
		unknown12a6 smallint not null,
		unknown12a7 smallint not null,
		unknown12a8 smallint not null,
		bEDITED tinyint not null,
		unknown12c tinyint not null,
		unknown12d1 smallint not null,
		unknown12d2 smallint not null,
		unknown12d3 smallint not null,
		unknown12d4 smallint not null,
		unknown12d5 smallint not null,
		unknown12d6 smallint not null,
		unknown12d7 smallint not null,
		unknown12d8 smallint not null,
		unknown12d9 smallint not null,
		unknown12d10 smallint not null,
		unknown12d11 smallint not null,
		unknown12d12 smallint not null,
		unknown12d13 smallint not null,
		unknown12d14 smallint not null,
		unknown12d15 smallint not null,
		unknown12d16 smallint not null,
		unknown12d17 smallint not null,
		unknown12d18 smallint not null,
		unknown12d19 smallint not null,
		unknown12d20 smallint not null,
		unknown12d21 smallint not null,
		unknown12d22 smallint not null,
		unknown12d23 smallint not null,
		unknown12d24 smallint not null,
		unknown12d25 smallint not null,
		unknown12d26 smallint not null,
		unknown12d27 smallint not null,
		unknown12d28 smallint not null,
		unknown12d29 smallint not null,
		unknown12d30 smallint not null,
		Ability1 smallint not null,
		Ability2 smallint not null,
		Ability3 smallint not null,
		Ability4 smallint not null,
		Ability5 smallint not null,
		Ability6 smallint not null,
		Ability7 smallint not null,
		Ability8 smallint not null,
		Ability9 smallint not null,
		Ability10 smallint not null,
		Ability11 smallint not null,
		Ability12 smallint not null,
		Ability13 smallint not null,
		Ability14 smallint not null,
		Ability15 smallint not null,
		Ability16 smallint not null,
		Ability17 smallint not null,
		Ability18 smallint not null,
		Ability19 smallint not null,
		Ability20 smallint not null,
		Ability21 smallint not null,
		Ability22 smallint not null,
		Ability23 smallint not null,
		Ability24 smallint not null,
		Ability25 smallint not null,
		Ability26 smallint not null,
		Ability27 smallint not null,
		Ability28 smallint not null,
		Ability29 smallint not null,
		Ability30 smallint not null,
		AbilityModifier1 smallint not null,
		AbilityModifier2 smallint not null,
		AbilityModifier3 smallint not null,
		AbilityModifier4 smallint not null,
		AbilityModifier5 smallint not null,
		AbilityModifier6 smallint not null,
		AbilityModifier7 smallint not null,
		AbilityModifier8 smallint not null,
		AbilityModifier9 smallint not null,
		AbilityModifier10 smallint not null,
		AbilityModifier11 smallint not null,
		AbilityModifier12 smallint not null,
		AbilityModifier13 smallint not null,
		AbilityModifier14 smallint not null,
		AbilityModifier15 smallint not null,
		AbilityModifier16 smallint not null,
		AbilityModifier17 smallint not null,
		AbilityModifier18 smallint not null,
		AbilityModifier19 smallint not null,
		AbilityModifier20 smallint not null,
		AbilityModifier21 smallint not null,
		AbilityModifier22 smallint not null,
		AbilityModifier23 smallint not null,
		AbilityModifier24 smallint not null,
		AbilityModifier25 smallint not null,
		AbilityModifier26 smallint not null,
		AbilityModifier27 smallint not null,
		AbilityModifier28 smallint not null,
		AbilityModifier29 smallint not null,
		AbilityModifier30 smallint not null,
		unknown13a smallint not null,
		unknown13b smallint not null,
		unknown13c smallint not null,
		unknown13d smallint not null,
		unknown13e smallint not null,
		unknown13f smallint not null,
		unknown13g smallint not null,
		CharLife int not null,
		unknown131 smallint not null,
		unknown132 smallint not null,
		unknown133 smallint not null,
		unknown134 smallint not null,
		unknown135 smallint not null,
		unknown136 smallint not null,
		unknown137 smallint not null,
		unknown138 smallint not null,
		unknown139 smallint not null,
		Bitmask1 tinyint not null,
		Bitmask2 tinyint not null,
		TestFlag1 tinyint not null,
		TestFlag2 tinyint not null,
		unknown14 smallint not null,
		unknown151 int not null,
		unknown152 int not null,
		unknown153 int not null,
		unknown154 int not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		//$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON user (number);";
		//if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN BANK UPDATE SCRIPT === */
	function update_banks($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table banks in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE banks IN DICTIONARY USING '".$tag."bank2.dat' (
			BBSName varchar(31) not null, ShopNumber int not null, Cash int not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		//$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON banks (BBSName);";
		//if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN ACTION UPDATE SCRIPT === */
	function update_actions($tag, $status) {
		//Revised and Untested
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table actions in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE actions IN DICTIONARY USING '".$tag."acts2.dat' (
			Name varchar(29) not null, SingleToUser VARCHAR(79) not null, SingleToRoom varchar(79) not null,
			UserToUser varchar(79) not null, UserToOtherUser varchar(79) not null, UserToRoom varchar(79) not null,
			MonsterToUser varchar(79) not null, MonsterToRoom varchar(79) not null, InventoryToUser varchar(79) not null,
			InventoryToRoom varchar(79) not null, FloorItemToUser varchar(79) not null, FloorItemToRoom varchar(79) not null,
			Offset utinyint not null, Blank varchar(98) not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON actions (Name);";
		if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN GANG UPDATE SCRIPT === */
	function update_gangs($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table gangs in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE gangs IN DICTIONARY USING '".$tag."gang2.dat' (
			KeyName varchar(19) not null, DisplayName varchar(19) not null, Exp uinteger not null,
			Leader varchar(29) not null, DateCreated integer not null,
			Members smallint not null, unknown2 int not null, unknown3 int not null,
			RollOver int not null, RollTimes smallint not null, unknown5 varchar(161) not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
	
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON gangs (KeyName);";
		if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN LIMITED UPDATE SCRIPT === */
	function update_limiteds($tag, $status) {
		
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table limiteds in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE limiteds IN DICTIONARY USING '".$tag."itow2.dat' (
			recnum int not null, itemnum int not null, offset int not null,
			mapnum int not null, roomnum int not null, username varchar(29) not null,
			unknown2 smallint not null,	monnum int not null, unknown3 varchar(15) not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
	
		$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON limiteds (recnum);";
		if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN UPDATES UPDATE SCRIPT === */
	function update_updates($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table updates in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE updates IN DICTIONARY USING '".$tag."upda2.dat' (
			RecordLength smallint not null, PageSizee smallint not null, IndexCount tinyint not null,
			FileVersion tinyint not null, Reserved1 tinyint not null, Reserved2 tinyint not null,
			Reserved3 tinyint not null, Reserved4 tinyint not null, FileFlags smallint not null,
			DuplicatePointCount tinyint not null, NotUsed tinyint not null, Allocation smallint not null,
			KeyPosition smallint not null, KeyLength smallint not null, KeyFlags smallint not null,
			Reserved21 tinyint not null, Reserved22 tinyint not null, Reserved23 tinyint not null,
			Reserved24 tinyint not null, ExtDataType tinyint not null, NullValue tinyint not null,
			NotUsed21 tinyint not null, NotUsed22 tinyint not null, ManKeyNumber tinyint not null,
			ACSNumber tinyint not null);";
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		//$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON updates (number);";
		//if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	/* === BEGIN CHARACTERS UPDATE SCRIPT === */
	function update_characters($tag, $status) {
		global $db;
		
		//If a previous table exists delete the old one
		if($status=="Update") {
			$sql = "drop table characters in dictionary;";
			if(!$db->dbexec($sql)) return "Error deleting old table.";
		}
		
		$sql = "CREATE TABLE characters IN DICTIONARY USING '".$tag."user2.dat' (
			userid varchar(29) not null, username varchar(29) not null, unsignedbinary uinteger not null,
			effect0 smallint not null, effect1 smallint not null, effect2 smallint not null, 
			effect3 smallint not null, effect4 smallint not null, effect5 smallint not null, 
			effect6 smallint not null, effect7 smallint not null, effect8 smallint not null, 
			effect9 smallint not null, effectval0 smallint not null, effectval1 smallint not null, 
			effectval2 smallint not null, effectval3 smallint not null, effectval4 smallint not null, 
			effectval5 smallint not null, effectval6 smallint not null, effectval7 smallint not null, 
			effectval8 smallint not null, effectval9 smallint not null, effectrnd0 smallint not null, 
			effectrnd1 smallint not null, effectrnd2 smallint not null, effectrnd3 smallint not null, 
			effectrnd4 smallint not null, effectrnd5 smallint not null, effectrnd6 smallint not null, 
			effectrnd7 smallint not null, effectrnd8 smallint not null, effectrnd9 smallint not null, 
			classtitle varchar(19) not null, race smallint not null, class smallint not null,
			lvl smallint not null, int smallint not null, wil smallint not null,
			str smallint not null, hea smallint not null, agi smallint not null, 
			cha smallint not null, currint smallint not null, currwil smallint not null, 
			currstr smallint not null, currhea smallint not null, curragi smallint not null, 
			currcha smallint not null, maxhp smallint not null, currhp smallint not null, 
			maxencumb smallint not null, currencumb smallint not null, unknown2 integer not null,
			unkonwn3 integer not null, unknown4 smallint not null, maxmagicres smallint not null, 
			currmagicres smallint not null, currmap integer not null, currroom integer not null,
			unknown5 varchar(11) not null, ";
			for($x=0;$x<100;$x++) $sql .= "item$x integer not null, ";
			for($x=0;$x<100;$x++) $sql .= "itemuse$x smallint not null, ";
			$sql .= "unknown6 integer not null, ";
			for($x=0;$x<50;$x++) $sql .= "itemkey$x integer not null, ";
			for($x=0;$x<50;$x++) $sql .= "itemkeyuse$x smallint not null, ";
			$sql .= "unknown7 varchar(15) not null, experience2 integer not null, experience integer not null, 
			unknown27 smallint not null, ";
			for($x=0;$x<100;$x++) $sql .= "spell$x smallint not null, ";
			$sql .= "evilpoints smallint not null, unknown8 smallint not null, unknown9 varchar(9) not null, ";
			for($x=0;$x<20;$x++) $sql .= "lastmap$x integer not null, ";
			for($x=0;$x<20;$x++) $sql .= "lastroom$x integer not null, ";
			$sql .= "unknown10 smallint not null, broadcast smallint not null, unknown11 smallint not null, 
			unknown18 smallint not null, perception smallint not null, stealth smallint not null, 
			martialarts smallint not null, thievery smallint not null, maxma smallint not null, 
			currma smallint not null, spellcasting smallint not null, traps smallint not null, 
			traps2 smallint not null, picklocks smallint not null, tracking smallint not null, 
			unknown23 smallint not null, runic integer not null, platinum integer not null, 
			gold integer not null, silver integer not null, copper integer not null, 
			wornweapon integer not null, unknown12 integer not null, ";
			for($x=0;$x<20;$x++) $sql .= "wornitem$x integer not null, ";
			$sql .= "unknown13 varchar(41) not null, lives integer not null, unknown14 varchar(29) not null, 
			gangname varchar(19) not null, unknown15 integer not null, unknown16 smallint not null, 
			cps smallint not null, suicide varchar(18) not null, unknown17 varchar(5) not null, 
			edited smallint not null, unknown24 varchar(60) not null, ";
			for($x=0;$x<30;$x++) $sql .= "abil$x smallint not null, ";	
			for($x=0;$x<30;$x++) $sql .= "abilval$x smallint not null, ";
			$sql .= "unknown25 varchar(55) not null);";
			
		if(!$db->dbexec($sql)) return "Error creating new table.";
		
		$sql = "select * from characters where username='Ghaleon'";
		$db->dbexec($sql);
		print_r($db->dump());
		//$sql = "CREATE UNIQUE NOT MODIFIABLE INDEX key0 ON updates (number);";
		//if(!$db->dbexec($sql)) return "Error creating new index table.";
		
		return "Update";
	}
	
	function view_config($table) {
		echo "<form action='".$_SERVER['PHP_SELF']."' method='POST'>";
		foreach($table as $entry){
			echo "<input type='submit' name='uid' value='".$entry['Button'].' '.$entry['Name']."' /> ";
		}
		echo "</form>";
	}
	
	function find_tables($utables) {
		/* Compares $utables with the current tables on the server and returns only
			the ones that are listed in both $utables and the server data. It will
			then return 'Existing' so that users know the database already exists. */
		global $db;
		
		$dbtables = $db->dbtables();
		foreach($utables as $i => $tables) {
			$table[$i]['Name'] = ucfirst($tables);
			if(array_search($tables, $dbtables)) {
				$table[$i]['Button'] = "Update"; //If Table was found
			}
			else {
				$table[$i]['Button'] = "Create"; //If Table was not found
			}
		}
	
		return $table;
	}
	
	$tag = "wcc";
	
	//Updatable tables - only allow users to update these tables [LOWERCASE ONLY]	
	$utables = array('races','classes','spells','monsters','items','shops','messages','textblocks','banks','actions','gangs','limiteds','characters');
	
	// Find Existing Tables
	$table = find_tables($utables);
	
	$post = explode(" ", $_POST['uid']);
	switch($post[1]) {
		case "Races":		$table[0]['Button'] = update_races($tag, $post[0]);			break;
		case "Classes":		$table[1]['Button'] = update_classes($tag, $post[0]);		break;
		case "Spells":		$table[2]['Button'] = update_spells($tag, $post[0]);			break;
		case "Monsters":	$table[3]['Button'] = update_monsters($tag, $post[0]);		break;
		case "Items":		$table[4]['Button'] = update_items($tag, $post[0]);			break;
		case "Shops":		$table[5]['Button'] = update_shops($tag, $post[0]);			break;	
		case "Messages":	$table[6]['Button'] = update_messages($tag, $post[0]);		break;
		case "Textblocks":	$table[7]['Button'] = update_textblocks($tag, $post[0]);		break;	
		case "Banks":		$table[8]['Button'] = update_banks($tag, $post[0]);			break;	
		case "Actions":		$table[9]['Button'] = update_actions($tag, $post[0]);		break;
		case "Gangs":		$table[10]['Button'] = update_gangs($tag, $post[0]);			break;	
		case "Limiteds":	$table[11]['Button'] = update_limiteds($tag, $post[0]);		break;	
		case "Characters":	$table[12]['Button'] = update_characters($tag, $post[0]);	break;
	}
	
	view_config($table); 
	
	$db->finish();
	
	?>

For each of the buttons, click Create {type}.

You can now go to Pervasive.SQL control panel to view the dats, insert, update, etc.  Theoretically, you can programmatically view the dats on-demand, and create cool dynamic pages like this: http://quicksilverbbs.com/monster.php?id=77
