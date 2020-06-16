pipeline {
    agent { node { label 'ctpd_ci' } }

    options {
        timeout(time: 20, unit: 'MINUTES') 
         ansiColor('gnome-terminal')
    }
    
    environment {
        
        ARTIFACTORY_CRED=credentials('artifactory_credentials')
        
    }

    parameters {
        choice(
            name: 'artifact',
            choices: "onos\nbackofficeFrontend\nprovisionerFrontend\nprovisionerBackend",
            description: 'which deprecated artifacted would you like to clean?' )
    }

    

    stages {

     
        stage('Init') {
             
             steps {

                
                sh '''
                        #!/bin/bash

                        rama="$brach"
                        artefacto="$artifact"
                        fecha_ref=$deprecated_date
                        url_api="http://artifactory.hi.inet/artifactory/api/storage/misc-oln/repo/"
                        url_base="http://artifactory.hi.inet/artifactory/misc-oln/repo/


                        anio_ref=$(echo $fecha_ref | awk -F'-' '{print $1}')
                        mes_ref=$(echo $fecha_ref | awk -F'-' '{print $2}')
                        dia_ref=$(echo $fecha_ref | awk -F'-' '{print $3}')


                        url_file=$url_api$artefacto/$rama

                        curl -v -X GET -u "$ARTIFACTORY_CRED" "$url_file" -o uri_file

                        grep "\"uri\"" uri_file | awk '{print $3}'| sed -e 's/"//' -e s'/"//' -e s'/,//' | grep "tar" > uri_tar

                        input="uri_tar"
                        while IFS= read -r line
                        do                            echo "$line"


                            item=$line

                            url_item=$url_file$item
                            curl -v -X GET -u "$ARTIFACTORY_CRED" "$url_item" -o item_file
                            echo $url_item
                            
                            url_delete=$url_base$artefacto/$rama$item

                            anio=$(grep "\\\"created\\\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /' |awk '{print $1}')
                            mes=$(grep "\"created\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /' |awk '{print $2}')
                            dia=$(grep "\"created\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /' |awk '{print $3}')
                            
                            
                            if [ "$anio_ref" -gt "$anio" ]; then

                                curl -v -X DELETE -u "$ARTIFACTORY_CRED" "$url_delete"
                                echo "borro por año"
                            
                            elif [ "$anio_ref" -eq "$anio" ] && [ "$mes_ref" -gt "$mes" ]; then

                                curl -v -X DELETE -u "$ARTIFACTORY_CRED" "$url_delete"
                                echo "borro por mes"

                            elif [ "$anio_ref" -eq "$anio" ] && [ "$mes_ref" -eq "$mes" ] && [ "$dia_ref" -gt "$dia" ]; then

                                curl -v -X DELETE -u "$ARTIFACTORY_CRED" "$url_delete"
                                echo "borro por dia"
                            fi

                            echo "termino $item"

                        done < "$input"

                '''
            }
        }
    }
}