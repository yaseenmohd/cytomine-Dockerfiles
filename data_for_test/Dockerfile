#
# Copyright (c) 2009-2017. Authors: see NOTICE file.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

FROM cytomine/java8:v1.1

MAINTAINER Cytomine Team "support@cytomine.be"

RUN apt-get update -y && apt-get install -y groovy
RUN cd /tmp/  && wget https://github.com/cytomine/Cytomine-java-client/releases/download/v1.1.2/cytomine-java-client.jar -O Cytomine-client-java.jar
RUN cd /tmp/ && wget "http://cytomine.be/release/demo_script.zip" -O demo_script.zip && unzip demo_script.zip

ADD injectdata.groovy /tmp/injectdata.groovy
RUN chmod +x /tmp/injectdata.groovy
ADD deploy.sh /tmp/deploy.sh
RUN chmod +x /tmp/deploy.sh

ENTRYPOINT ["/tmp/deploy.sh"]
