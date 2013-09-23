<?php

$fp_r=fopen('plist-cat-sorted.csv','r') or die('what the hell');
$products=array();

fgetcsv($fp_r);

while($data=fgetcsv($fp_r,null,',')){
	$product=array();
	$product['catagory']=$data[0];
	$product['brand']=replace_and_explode($data[1])[1];
	$product['unit']=replace_and_explode($data[2])[1];
	for($i=0;$i<3;$i++)
		unset($data[$i]);
	$attrs=array();
	foreach($data as $attr){
		$attr_exploded=replace_and_explode($attr);
		if(count($attr_exploded)===2)
			$attrs[$attr_exploded[0]]=$attr_exploded[1];
	}
	$product['attrs']=$attrs;
	$products[]=$product;
	
}//end of while

fclose($fp_r);

$units=$catagories=$brands=array();

foreach($products as $product){
	$units[$product['unit']]['id']=false;
	$brands[$product['brand']]['id']=false;
	$catagories[$product['catagory']]['id']=false;
}

$mysqli = new mysqli("localhost", "root", "sahib", "test");

foreach($units as $unit=>$value){
	$sql = "INSERT INTO `units` (`unit_name`) VALUES ('$unit')";
	$mysqli->query($sql);
	$units[$unit]['id']= $mysqli->insert_id;
}

foreach($brands as $brand=>$value){
	$sql = "INSERT INTO `manufacturers` (`name`) VALUES ('$brand')";
	$mysqli->query($sql);
	$brands[$brand]['id']= $mysqli->insert_id;
}

foreach($catagories as $catagory=>$value){
	$sql = "INSERT INTO `catagories` (`name`) VALUES ('$catagory')";
	$mysqli->query($sql);
	$catagories[$catagory]['id']= $mysqli->insert_id;
}

foreach($products as $product){
	$cat_id=$catagories[$product['catagory']]['id'];
	$unit_id=$units[$product['unit']]['id'];
	$brand_id=$brands[$product['brand']]['id'];
	$attrs='';
	if($product['attrs'])
		$attrs=json_encode($product['attrs']);
	if($product['brand']==='Unknown')
		$name=$product['catagory'];
	else $name=$product['brand'].'-'.$product['catagory'];
	foreach($product['attrs'] as $attr=>$value){
		$name.='-'.$value;
	}
	
	$sql = "INSERT INTO `products` (`name`,`manufacturer_id`,`catagory_id`,`unit_id`,`attribute_1`) 
		VALUES ('$name',$brand_id,$cat_id,$unit_id,'$attrs')";
	$mysqli->query($sql);
	echo $sql.'\n';
	echo $mysqli->error.'\n';
}


$mysqli->close();

function replace_and_explode($target){
	$target=str_replace(array('(',')') , '' , $target);
	return explode('=',$target);
}

