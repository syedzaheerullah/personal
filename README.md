#!/bin/bash
cidr=$1
number_of_subnets=$2
network_IP=`echo $1 | cut -f 1 -d '/'`
subnet_mask=`echo $1 | cut -f 2 -d '/'`
last_bit=`echo $network_IP | cut -f 4 -d '.'`
first_bits=`echo $network_IP | cut -d '.' -f1-3`

new_mask=$(( 32 - $subnet_mask))
maximum_addressess=$((2 ** $new_mask))

#Looping over possible combinations
split()
{
power=2
initial=1
range=$1
while [ $power -lt $range ]
do
        initial=$(($initial+1))
        power=$((2**$initial))
done
}
subnet_last_bit=$last_bit
subnet_address=0

while [ $number_of_subnets -gt 0 ]
do
        last_bit=$(( $last_bit + $subnet_address ))
        range=$(( $maximum_addressess /  $number_of_subnets ))
        split $range
        subnet_address=$(( 2 ** $initial ))
        subnet_last_bit=$(( $subnet_last_bit + $subnet_address ))
        maximum_addressess=$(( $maximum_addressess - $subnet_address ))
        d_bit=$(( $subnet_last_bit - 1))
        mask=$(( $new_mask - $initial + $subnet_mask ))
        gateway_bit=$(( $last_bit + 1 ))
        number_of_hosts=$(( $subnet_address - 3 ))
        echo "Subnet= $first_bits.$last_bit/$mask Network=$first_bits.$last_bit Broadcast=$first_bits.$d_bit Gateway= $first_bits.$gateway_bit Hosts=$number_of_hosts"
        number_of_subnets=$(($number_of_subnets -1 ))
done
