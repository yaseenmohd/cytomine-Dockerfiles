#
# Copyright (c) 2009-2020. Authors: see NOTICE file.
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

FROM cytomine/iipbase:v1.2.0

MAINTAINER Cytomine SCRLFS "support@cytomine.coop"

#openslide on cytomine repo
RUN cd /tmp && git clone https://github.com/cytomine/openslide && cd /tmp/openslide && git checkout 3a284f9ab4fb39f5081f61f1b0169cdda8dde962 && autoreconf -i && ./configure && make && make install

#IIP Image Server
RUN cd /tmp && git clone https://github.com/cytomine/iipsrv && cd /tmp/iipsrv && git checkout f7ae827c49cac8420bed9ffb25edc01d88b3b8ff && sh autogen.sh && LDFLAGS="-L/usr/local/lib -lopenslide" CPPFLAGS="-I/usr/local/include/openslide" ./configure && make
RUN mkdir -p /usr/local/httpd/fcgi-bin && cp /tmp/iipsrv/src/iipsrv.fcgi /usr/local/httpd/fcgi-bin/iipsrv.fcgi

RUN ldconfig -v

ENTRYPOINT ["/tmp/deploy.sh"]

