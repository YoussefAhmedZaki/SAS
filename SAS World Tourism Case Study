/*note : Before Run the Program , Make sure to import Tourism Data in your Library*/
/*creating the continent Format*/
proc format;
	value continents 1="North America" 2="South America" 3="Europe" 4="Africa" 
		5="Asia" 6="Oceania" 7="Antarctica";
run;

/*creating a target table called claned_tourism*/
Data Work.cleaned_tourism;
	/*Reordering the columns*/
	retain Country_Name Tourism_Type Category Series Y2014 Continent;
	set work.TOURISM (drop=_1995 --_2013);
	length Country_Name $200 Tourism_Type $30;
	retain Country_Name "" Tourism_Type "";

	/*Extracting Data in Column A and storing it in new columns (Country_Name , Tourism_Type) */
	if A ^=. then
		Country_Name=Country;

	if lowcase(Country)="inbound tourism" then
		Tourism_Type="Inbound tourism";
	else if lowcase(Country)='outbound tourism' then
		Tourism_Type="Outbound tourism";

	if Country_Name ^=Country and Country ne Tourism_Type;
	Series=upcase(Series);

	if Series=".." then
		Series=" ";

	/*Making A variable to use it in if statement to make currency conversion*/
	currency_in=strip(scan(country, -1, ' '));

	if _2014='..' then
		_2014='.';

	if currency_in='Mn' then
		do;

			if input(_2014, 16.) ne . then
				Y2014=input(_2014, 16.)*1000000;
			else
				Y2014=.;
		end;
	else if currency_in='Thousands' then
		do;

			if input(_2014, 16.) ne . then
				Y2014=input(_2014, 16.)*1000;
			else
				Y2014=.;
		end;

	if currency_in='Mn' then
		Category=cat(scan(country, 1, '-', 'r'), " - US$");
	else if currency_in='Thousands' then
		Category=scan(country, 1, '-', 'r');
	format Y2014 comma25.;
	drop A currency_in Country _2014;
run;

/*Sorting Data in Country_info Table on Country Name*/
proc sort data=work.country_info(rename=(Country=Country_Name)) 
		out=Country_Sorted;
	by Country_Name;
run;

/* Createing Two output Tables */
data Final_Tourism NoCountryFound(keep=Country_Name);
	merge cleaned_tourism(in=t) country_sorted(in=c);
	by country_name;

	if t=1 and c=1 then
		output Final_Tourism;

	if (t=1 and c=0) and first.country_name then
		output NoCountryFound;
	format Continent continents.;
run;

