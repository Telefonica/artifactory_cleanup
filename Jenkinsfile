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
                            
                            cat item_file
                            anio_2=$(grep "\\\"created\\\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /')
                            anio=$(grep "\\\"created\\\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /' |awk '{print $1}')
                            mes=$(grep "\"created\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /' |awk '{print $2}')
                            dia=$(grep "\"created\"" item_file | awk '{print $3}'| sed -e 's/-/ /' -e 's/-/ /' -e 's/T/ /' -e 's/"/ /' |awk '{print $3}')
                            
                            echo $anio_2
                            echo $anio
                            echo $anio_ref
                            if [ "$anio_ref" -gt "$anio" ]; then

                                #curl -v -X DELETE -u "$ARTIFACTORY_CRED" "$url_item"
                                echo "borro por año"
                            
                            elif [ "$anio_ref" == "$anio" ] && [ "$mes_ref" > "$mes" ]; then

                                #curl -v -X DELETE -u "$ARTIFACTORY_CRED" "$url_item"
                                echo "borro por mes"

                            elif [ "$anio_ref" == "$anio" ] && [ "$mes_ref" == "$mes" ] && [ "$dia_ref" > "$dia" ]; then

                                #curl -v -X DELETE -u "$ARTIFACTORY_CRED" "$url_item"
                                echo "borro por dia"
                            fi

                            echo "termino $item"

                        done < "$input"

                '''
            }
        }
    }
}