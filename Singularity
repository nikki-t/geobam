Bootstrap: docker
From: ubuntu:20.04

%files
    run_geobamdata.R /opt/confluence/

%post
    apt update
	echo "America/New_York" | tee /etc/timezone
	DEBIAN_FRONTEND=noninteractive apt install -y tzdata
	apt -y install locales gnupg software-properties-common build-essential libcurl4-gnutls-dev libxml2-dev libssl-dev gcc gfortran libnetcdf-dev libnetcdff-dev
	locale-gen en_US.UTF-8	
	apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
	echo 'deb https://cloud.r-project.org/bin/linux/ubuntu focal-cran40/' >> /etc/apt/sources.list
	apt update
	apt -y install r-base r-base-dev
	/usr/bin/Rscript -e 'install.packages("devtools", dependencies = TRUE, repos = "http://cran.rstudio.com/")'
	/usr/bin/Rscript -e 'Sys.setenv(DOWNLOAD_STATIC_LIBV8 = 1); install.packages("V8")'
	/usr/bin/Rscript -e 'install.packages("dplyr", dependencies = TRUE, repos = "http://cran.rstudio.com/")'
	/usr/bin/Rscript -e 'install.packages("reshape2", dependencies = TRUE, repos = "http://cran.rstudio.com/")'
	/usr/bin/Rscript -e 'install.packages("settings", dependencies = TRUE, repos = "http://cran.rstudio.com/")'
	/usr/bin/Rscript -e 'devtools:: install_github("craigbrinkerhoff/geoBAMr", force = TRUE)'
	/usr/bin/Rscript -e 'devtools:: install_github("SWOT-Confluence/geobamdata", force = TRUE, dependencies = TRUE)'
	mkdir -p /opt/confluence/input
	mkdir /opt/confluence/output
	chmod 755 /opt/confluence/run_geobamdata.R

%runscript
    /usr/bin/Rscript /opt/confluence/run_geobamdata.R

%test
	gbd_pkg=$(/usr/bin/Rscript -e 'installed.packages()' | grep geobamdata)
	if [[ -z $gbd_pkg ]]; then
		echo "geobamdata was not successfully installed."
		exit 1
	fi

	in_dir=/opt/confluence/input
	out_dir=/opt/confluence/output
	file=/opt/confluence/run_geobamdata.R
	if [[ ! -d "$in_dir" ]] || [[ ! -d "$out_dir" ]] || [[ ! -f "$file" ]]; then
		echo "confluence directories and/or script file were not properly created."
		exit 1
	fi

	echo "Passed all tests."

%labels
    Author ntebaldi@umass.edu
    Version v0.0.1
	Name geobam

%help
    This container runs geoBAMr on data found in the input directory and outputs a NetCDF file to the output directory.
