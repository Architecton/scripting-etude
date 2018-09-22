# TODO: 14.4.2017 - fix tocke (see solution), fix prostor (work with drevo)
# Good luck! ;)

RANDOM=42

# get_gcd: izracunaj gcd dveh stevil podanih kot parametra z uporabo Evklidovega algoritma
function get_gcd()
{
	if [ $1 -gt $2 ]
	then
		a=$1
		b=$2
	else
		a=$2
		b=$1
	fi
	
	while (( $a % $b != 0 ))
	do
		c=$(($a % $b))
		a=$b
		b=$c
	done

	echo $b

}

# fib: izracunaj fibonaccijevo stevilo s podanim indeksom stevila
function fib()
{
	a=0
	b=1
	n=$1
	if [ $n -eq 0 ]
		then
		echo 0
	elif [ $n -eq 1 ]
		then
		echo 1
	else
		# dinamicni nacin - zgradi od spodaj navzgor
		for ((i = 1; i < n; i++)) do
			c=$((a+b))
			a=$b
			b=$c
		done
		echo $c
	fi
}

function drevo()
{
	local address="$1"
	local max_depth=$2
	local current_depth=$3

	local brackets=""
	for (( i = 0; i < current_depth; i++ )) ; do
		local brackets="$brackets----"
	done

	local files="$(ls -Q "$address")"
	eval file_arr="($files)"

	local f
	for f in "${file_arr[@]}" ; do
		local file_type_raw=$(stat --printf "%F\n" "$address""/$f")
		local temp_arr=($file_type_raw)
		local file_type=${temp_arr[0]}


		if [[ "$file_type" == "directory" ]] ; then
			printf "%s%s %s\n" "$brackets" "DIR  " "$f"
			local new_address="$address""/$f"
			if (( current_depth < max_depth )) ; then
				# rekurzivni klic za najden direktorij
				drevo "$new_address" $max_depth $((current_depth+1))
			fi
			
		fi

		# uporabi ustrezen izpis glede na tip
		if [ "$file_type" == "socket" ] ; then
			# printf "%s" "$brackets"
			printf "%s%s %s\n" "$brackets" "SOCK " "$f"
		elif [ "$file_type" == "symbolic" ] ; then
			printf "%s%s %s\n" "$brackets" "LINK " "$f"
		elif [ "$file_type" == "regular" ] ; then
			printf "%s%s %s\n" "$brackets" "FILE " "$f"
		elif [ "$file_type" == "character" ] ; then
			printf "%s%s %s\n" "$brackets" "CHAR " "$f"
		elif [ "$file_type" == "block" ] ; then
			printf "%s%s %s\n" "$brackets" "BLOCK" "$f"
		elif [ "$file_type" == "fifo" ] ; then
			printf "%s%s %s\n" "$brackets" "PIPE " "$f"
		fi

	done
}

# velikost: izracunaj velikosti vseh datotek, ki so vsebovane v direktoriju podanem kot argument
function velikost()
{
	# Shrani naslov in ostale vhodne podatke.
	local address="$1"
	local max_depth=$2
	local current_depth=$3
	local size_sum=$4

	# Shrani datoteke v direktoriju.
	local files="$(ls -Q "$address")"
	eval file_arr="($files)"

	# lokalna iteracijska spremenljivka
	local f
	for f in "${file_arr[@]}" ; do
		# Izlusci tip datoteke.
		local file_type_raw=$(stat --printf "%F\n" "$address""/$f")
		local temp_arr=($file_type_raw)
		local file_type=${temp_arr[0]}

		# ce je datoteka direktorij
		if [[ "$file_type" == "directory" ]] ; then
			# Izracunaj nov naslov (za znotraj tega direktorija).
			local new_address="$address""/$f"
			if (( current_depth < max_depth )) ; then
				# rekurziven klic za najden direktorij
				local size_sum=$(velikost "$new_address" $max_depth $(( current_depth + 1 )) $size_sum)
			fi

			# K sestevku velikosti dodaj velikost direktorija.
			local size_folder=$(stat --printf="%s" "$address""/""$f")
			local size_sum=$((size_sum + size_folder))
		else
			# izracunaj velikost datoteke in jo pristej k celotnemu sestevku
			local size=$(stat --printf="%s" "$address""/$f")
			local size_sum=$((size_sum + size))
			size=0
		fi
	done
	echo $size_sum
}

# bloki: Izracunaj stevilo blokov, ki jih zasedajo datoteke v direktoriju podanem kot argument
function bloki()
{
	# Shrani naslov in ostale vhodne podatke.
	local address="$1"
	local max_depth=$2
	local current_depth=$3
	local blocks_sum=$4

	# Shrani datoteke v direktoriju.
	local files="$(ls -Q "$address")"
	eval file_arr="($files)"

	local f
	# lokalna iteracijska spremenljivka
	for f in "${file_arr[@]}" ; do

		# Izlusci tip datoteke.
		local file_type_raw=$(stat --printf "%F\n" "$address""/$f")
		local temp_arr=($file_type_raw)
		local file_type=${temp_arr[0]}

		# ce je datoteka direktorij
		if [[ "$file_type" == "directory" ]] ; then
			# Izracunaj nov naslov (za znotraj tega direktorija).
			local new_address="$address""/$f"

			# rekurziven klic za najden direktorij
			local blocks_sum=$(bloki "$new_address" $max_depth $(( current_depth + 1 )) $blocks_sum)

			# K sestevku blokov dodaj bloke direktorija.
			local blocks_folder=$(stat --printf="%b" "$address""/$f")
			local blocks_sum=$((blocks_sum + blocks_folder))
		else
			# izracunaj stevilo blokov, ki jih zaseda datoteka in jo pristej k celotnemu sestevku
			local blocks=$(stat --printf="%b" "$address""/$f")
			local blocks_sum=$((blocks_sum + blocks))
			blocks=0
		fi
	done
	echo $blocks_sum
}

# bloki_velikosti: Izracunaj velikost vseh blokov, ki jih zasedajo datoteke v direktoriju podanem kot argumenti
function bloki_velikost()
{
	# Shrani naslov in ostale vhodne podatke.
	local address="$1"
	local max_depth=$2
	local current_depth=$3
	local bsize_sum=$4

	# Shrani datoteke v direktoriju.
	local files="$(ls -Q "$address")"
	eval file_arr="($files)"

	# lokalna iteracijska spremenljivka
	local f

	for f in "${file_arr[@]}" ; do
		# Izlusci tip datoteke.
		local file_type_raw=$(stat --printf "%F\n" "$address""/$f")
		local temp_arr=($file_type_raw)
		local file_type=${temp_arr[0]}

		# ce je datoteka direktorij
		if [[ "$file_type" == "directory" ]] ; then
			# Izracunaj nov naslov (za znotraj tega direktorija).
			local new_address="$address""/$f"

			# rekurziven klic za najden direktorij
			local bsize_sum=$(bloki_velikost "$new_address" $max_depth $(( current_depth + 1 )) $bsize_sum)

			# Izracunaj stevilo blokov, ki jih zavzema najden direktorij.
			local blocks_folder=$(stat --printf="%b" "$address""/$f")
			# Izracunaj velikost posameznega bloka.
			local size_block_folder=$(stat --printf="%B" "$address""/$f")
			# Izracunaj velikost vseh blokov.
			local bsize_folder=$(( blocks_folder * size_block_folder ))
			# Izracunano velikost pristej k celotnemu sestevku.
			local bsize_sum=$((bsize_sum + bsize_folder))
		else
			# Izracunaj stevilo blokov iz katerih je najdena datoteka.
			local num_blocks=$(stat --printf="%b" "$address""/$f")
			# Izracunaj velikost posameznega bloka.
			local block_size=$(stat --printf="%B" "$address""/$f")
			# Izracunaj celotno velikost vseh blokov in jo pristej k celotnem sestevku.
			local bsize=$(( num_blocks * block_size ))
			local bsize_sum=$(( bsize_sum + bsize ))
			bsize=0
		fi
	done
	echo $bsize_sum
}

action=${1}

# izpis pomoci
if [ "$action" == "pomoc" ]
then
	printf "Uporaba: %s akcija parametri\n" "$0"

# izhodni status na gcd 2. in 3. parametra
elif [ "$action" == "status" ]
then
	res=$(get_gcd $2 $3)
	exit $res

# pojdi cez leta v parametrih 2..n in preveri, ce so prestopna leta
elif [ "$action" == "leto" ]
	then
	# Pojdi cez argumente, ki oznacujejo leta
	for ((i = 2; i <= $#; i++))
		do
		# leto := i-ti argument
		leto=${!i}
		# Uporabi formulo za izracun prestopnosti in izpisi rezultat v zahtevanem formatu.
		if ((leto % 4 == 0))
			then
			if ((leto % 100 == 0))
				then
				if ((leto % 400 == 0))
					then
					printf "Leto %s je prestopno.\n" "$leto"
				else
					printf "Leto %s ni prestopno.\n" "$leto"
				fi
			else
				printf "Leto %s je prestopno.\n" "$leto"
			fi
		else
			printf "Leto %s ni prestopno.\n" "$leto"
		fi
	done


# pojdi cez parametre 2..n in za vsakega izpisi fibonaccijevo stevilo, ki ima indeks enak vrednosti tega parametra
elif [ "$action" == "fib" ]
then
	# Pojdi cez argumente, ki predstavljajo indekse Fibonaccijevih stevil.
	for ((i = 2; i <= $#; i++)) do
		res=$(fib ${!i})
		printf "%s: %s\n" "${!i}" "$res"
	done
	
elif [ "$action" == "userinfo" ]
then
	# pojdi cez parametre, ki predstavljajo uporabnike
	for ((i = 2; i <= $#; i++)) do
		# izpisi ime uporabnika podanega kot i-ti parameter
		printf "%s: " "${!i}"

		# //// Preveri, ce uporabnik obstaja (pregled datoteko passwd) in analiziraj izhodni status.
		getent passwd ${!i} >/dev/null
		# Ce izhodni status ni 0 izpisi err in pojdi na naslednjega uporabnika.
		if (( $? != 0 )) ; then
			printf "err\n"
			continue
		fi

		# shrani uid in gid uporabnika
		user_id=$(id -u ${!i})
		group_id=$(id -g ${!i})

		# preveri, ce sta enaka
		if ((user_id == group_id))
			then
			printf "enaka "
		fi

		# Shrani oba mozna domaca direktorija.
		dir_1="/home/${!i}"
		dir_2="/home/uni/${!i}"

		# ce kateri od njiju obstaja
		if [[ -d "$dir_1" || -d "$dir_1" ]] ; then
			printf "obstaja "
		fi

		# Shrani skupine, ki jim podani uporabnik pripada.
		user_groups=$(id -G ${!i})
		counter=0
		# Prestej skupine in izpisi stevilo.
		for number in $user_groups ; do
			counter=$((counter+1))
		done
		printf "%s" "$counter"
		printf "\n"
	done

# funkcionalnost tocke (glej navodila)
elif [ "$action" == "tocke" ]
then
	# nastavi seme za spremenljivko RANDOM na 42.
	
	# stevca vseh tock in stevila studentov (== st. vrstic v zapisu)
	grand_total=0
	line_counter=0
	while read line ; do

		# Preveri, ce je prvi znak v vrstici # (gre za komentar - ignoriraj).
		first=${line:0:1}
		if [ "$first" == "#" ] ; then
			continue
		fi

		# povecaj stevec studentov
		line_counter=$(( line_counter + 1 ))

		# vsebino vrstice loci po presledkih in shrani v tabelo
		line_contents=($line)
		len=${#line_contents[@]}

		# Shrani vpisno stevilko trenutnega studenta.
		student_id=${line_contents[0]}
		# Shrani tretjo in cetrto stevko vpisne stevilke.
		third_fourth=${student_id:2:2}

		# Preveri, je tip == p oz. tip == P.
		p=0
		if ((len == 5)) ; then
			if [[ "${line_contents[4]}" == "p" || "${line_contents[4]}" == "P" ]] ; then
				p=1
			fi
		fi

		# Sestej tocke trenutnega studenta.
		sum=0;
		for (( i = 1; i < 4; i++)) ; do
			sum=$((sum + ${line_contents[$i]} ))
		done

		# Ce tip == p oz. tip == P, deli sestevek z 2.
		if (( p == 1 )) ; then
			sum=$(( sum / 2 ))
		# Ce sta tretja in cetrta stevka vpisne stevilke enaki 14.
		# Pristej vsoti nakljucno stevilo iz [1, 5].
		elif (( third_fourth == 14 )) ; then
			sum=$(( sum + (RANDOM % 5) + 1 ))
		fi

		# Sestevek tock je najvec 50.
		if (( sum > 50)) ; then
			sum=50
		fi

		# Pristej vsoto studenta k celotni vsoti.
		grand_total=$(( grand_total + sum ))

		# Izpisi vpisno stevilko in sestevek tock.
		printf "%s: " "$student_id"
		printf "%s" "$sum"
		printf "\n"

	done

	# Izracunaj povprecno stevilo tock.
	avg_points=$(( grand_total / line_counter ))

	# Izpisi stevilo studentov in povprecno stevilo dosezenih tock
	printf "St. studentov: %s\n" "$line_counter"
	printf "Povprecne tocke: %s\n" "$avg_points"
# izpis drevesne strukture podanega direktorija
elif [ "$action" == "drevo" ]; then
	max_depth=3
	# Shrani ciljni naslov.
	if (( $# == 1 )); then
		target_address="$PWD"
	elif [[ $# -eq 2 && $2 =~ ^-?[0-9]+$ ]] ; then
		max_depth=$2
		target_address="$PWD"
	else
		target_address=""
		for (( i = 2; i <= $#; i++ )); do
			if [[ ${!i} =~ ^-?[0-9]+$ ]] ; then
				max_depth=${!i} 
			else
				target_address="$target_address ${!i}"
			fi
		done
		target_address="$(echo -e "${target_address}" | sed -e 's/^[[:space:]]*//')"

	fi
	printf "DIR   %s\n" "$target_address"
	drevo "$target_address" $max_depth 1
	
# izpis prostorskih statistik podanega direktorija
elif [ "$action" == "prostor" ]; then
	max_depth=3
	# Shrani ciljni naslov.
	if (( $# == 1 )); then
		target_address=$(echo "$PWD")
	elif [[ $# -eq 2 && $2 =~ ^-?[0-9]+$ ]] ; then
		max_depth=$2
		target_address="$PWD"
	else
		target_address=""
		for (( i = 2; i <= $#; i++ )); do
			if [[ ${!i} =~ ^-?[0-9]+$ ]] ; then
				max_depth=${!i}
			else
				target_address="$target_address ${!i}"
			fi
		done
		target_address="$(echo -e "${target_address}" | sed -e 's/^[[:space:]]*//')"

	fi


	# izracunaj rezultat za podani direktorij
	res=$(velikost "$target_address" $max_depth 0 0)
	# pristej se statistiko podanega direktorija samega
	size_folder=$(stat --printf="%s" "$target_address")
	res=$(( res + size_folder ))
	printf "Velikost: %s\n" "$res"

	# izracunaj rezultat za podani direktorij
	res=$(bloki "$target_address" $max_depth 0 0)
	# pristej se statistiko podanega direktorija samega
	blocks_folder=$(stat --printf="%b" "$target_address")
	res=$(( res + blocks_folder ))
	printf "Blokov: %s\n" "$res"

	# izracunaj rezultat za podani direktorij
	res=$(bloki_velikost "$target_address" $max_depth 0 0)
	# pristej se statistiko podanega direktorija samega
	blocks_folder=$(stat --printf="%b" "$target_address")
	size_block_folder=$(stat --printf="%B" "$target_address")
	bsize_folder=$(( blocks_folder * size_block_folder ))
	res=$(( res + bsize_folder ))
	printf "Prostor: %s\n" "$res"
# ob napacno podanih parametrih
else
	printf "Napacna uporaba skripte!\n"
	printf "Uporaba: %s akcija parametri\n" "$0"
	exit 42
fi 