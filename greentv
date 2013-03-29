#!/bin/bash
#
#
# Created by MrMen <tetcheve at gmail dot com>
# First time Edited on 02-01-2012
#
# Time-stamp: <29-03-2013 12:09:39>
# 


function usage (){
    echo "Usage of "$0" :"
    echo " "$0" package"
    echo "Give one argument (name of an aur package) to this script and it will let you"
    echo " know more about its infos."
    echo ""
    echo "Actually nothing work at the moment"
    echo "It's a dev version"
}



LIST=liste.json
AVAIL_LIST=chaine

echo ":: get the list"
# get the list
if [ ! -e $LIST ]; then
    curl -s http://chaines-tv.orange.fr/pfs-webapp/live/servicePlan.json -o temp.gz
    gzip -d temp.gz
    mv temp $LIST
fi

# if existing remove list of channels
if [ -e $AVAIL_LIST ]; then
    rm $AVAIL_LIST
fi


echo ":: parse the list to get name and channel"
# parse the list to get name and channel
cat $LIST | sed 's/useUrlSigning/æ/g' | tr 'æ' '\n' | sed 's/.*"name"://g; s/\("[a-zA-Z0-9 ]*"\)\(.*channelId":\)\([0-9]*\)\(.*\)/\1 \3/g;' > $AVAIL_LIST

echo ":: create filter for choosing your channel : only tnt"
# create filter for choosing your channel : only tnt
touch liste
for i in 'TF1' '\"France 2\"' 'France 3' 'France 5' 'Arte' '\"M6\"' 'Direct 8' 'W9' 'TMC' 'NT1' 'NRJ12' 'LCP' 'France 4' 'BFM TV' 'I. Télé' 'Direct Star' 'Gulli' 'France O' 'France 24'; do grep "$i" $AVAIL_LIST >> liste; done

# sort with --uniq option
cat liste | sort --uniq > temp
cat temp
cat temp | sed 's/."programs"//g' > liste
rm temp
MAX=$(wc -l liste | awk '{print $1}')

# clear 
#clear

# print all information
#awk  -F "\"" -v var=1 '{print var"  -->  " $2; var++}' liste

# echo "Please choose a channel :"
# read channel

# if (($channel<1)); then
#     echo "Error : out of range. Exiting..."
#     exit 1
# elif (($channel>$MAX)); then
#     echo "Error : out of range. Exiting..."
#     exit 1
# fi

# convert channel to real channel
#real_channel=$(cat liste | head -n $channel | tail -1 | awk -F "\"" '{print $3}' | sed 's/ //g')


echo ":: generate playlist"
# avoid problems with space separator
OLD_IFS=$IFS
IFS=$(echo -ne "\n\b")

if [ -e playlist.m3u ]; then
    rm playlist.m3u
fi;

touch playlist.m3u
echo "##EXTM3U" > playlist.m3u
value=1
for i in $(cat liste);do
    channel_name=$(echo $i | awk -F "\"" '{print $2}')
    channel_number=$(echo $i | awk -F "\"" '{print $3}' | sed 's/ //g')
    curl -s "http://chaines-tv.orange.fr/pfs-webapp/user/live/channel/${channel_number}/url.json?resolution=MEDIUM&subtitle=false" -o out 
    adress=$(cat out | sed 's/http:\/\//mmsh:\/\/CDLYOS02.se./g; s/\(.*\)\(mmsh.*\)\(",.*\)/\2/g')
    echo "#EXTINF:0,$value. $channel_name" >> playlist.m3u
    echo $adress >> playlist.m3u
    value=$(($value+1))
done

# restore ifs
IFS=$OLD_IFS


echo ":: restoring IFS"
echo ":: exit"

exit 0



# valid channel so get link
curl -s "http://chaines-tv.orange.fr/pfs-webapp/user/live/channel/${real_channel}/url.json?resolution=LOW&subtitle=false" -o out 
adress=$(cat out | sed 's/http:\/\//mmsh:\/\/CDLYOS02.se./g; s/\(.*\)\(mmsh.*\)\(",.*\)/\2/g')

/Applications/VLC.app/Contents/MacOS/VLC "$adress" 



exit 0
