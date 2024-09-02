pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                // Perform a shallow clone with only the latest commit
                sh '''
                    pwd
                    ls
                   if [ ! -d "odoo" ]; then
                       git clone --depth 1 --branch 17.0 --recurse-submodules https://github.com/He-Is-HaZaRdOuS/odoo.git
                   else
                       cd odoo
                       git fetch --depth 1 origin 17.0
                       git reset --hard FETCH_HEAD
                       git submodule update --init --recursive --depth 1
                   fi
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    echo 'jenkins'| sudo -S apt-get update
                    echo 'jenkins'| sudo -S apt-get install -y python3 python3-pip postgresql libldap2-dev libsasl2-dev libpq-dev
                '''
            }
        }
        stage('Configure PostgreSQL') {
            steps {
                script {
                    // Start PostgreSQL service
                    sh '''
                         echo 'jenkins' | sudo -S service postgresql start
                    '''

                    // Check if the 'jenkins' role exists
                    def roleExists = sh(script: 'echo jenkins | sudo -S -u postgres psql -tAc "SELECT 1 FROM pg_roles WHERE rolname=\'jenkins\'"', returnStdout: true).trim()

                    if (roleExists == '1') {
                        echo "Role 'jenkins' already exists."
                    } else {
                        // Create the 'jenkins' role with specific permissions
                        sh '''
                        echo 'jenkins' | sudo -S -u postgres createuser --createdb jenkins
                        '''
                    }

                    sh '''
                        echo 'jenkins' | sudo -S -u postgres psql -c "ALTER USER jenkins CREATEDB;"
                    '''

                }
            }
        }
        stage('Configure Odoo') {
            steps {
                sh '''
                    cd odoo/
                    pip3 install -r requirements.txt --break-system-packages
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                    cd odoo/
                    ./odoo-bin --addons-path="addons/, tutorials/" -d rd-demo -i real_estate,real_estate_account --stop-after-init --test-file=tutorials/real_estate/tests/test_main.py --config=./debian/odoo.conf -s
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    cd odoo/
                    ./odoo-bin --addons-path="addons/, tutorials/" -d rd-demo -i real_estate,real_estate_account --config=./debian/odoo.conf -s
                '''
            }
        }
    }
}
