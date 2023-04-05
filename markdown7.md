# Day 7
Logstash
server side data processing pipeline
ingest multiple data sources simultaneously
transform events into common formats
enrich events with additional information
output final results to a variety of destinations

Elasticsearch
sudo systemctl stop logstash
cd /etc/logstash/
ll
vi pipelines.yml
sudo mkdir my-pipeline
sudo chown logstash: my-pipeline/
vi pipeline.yml:

comment out the two lines. and add 
pipeline.id: test
path.config: "/etc/logstash/my-pipeline/*.conf"
:w !sudo tee %
:q!

cd my-pipeline
vi input.conf
insert..input {}
:w !sudo tee %
:q!

do it for filter.conf and output.conf

message is from copy from zeek http logs 
for input {
    generator {
        message => '{"ts":1591367999.512593,"uid":"C5bLoe2Mvxqhawzqqd","id.orig_h":"192.168.4.76","id.orig_p":46378,"id.resp_h":"31.3.245.133","id.resp_p":80,"trans_depth":1,"method":"GET","host":"testmyids.com","uri":"/","version":"1.1","user_agent":"curl/7.47.0","request_body_len":0,"response_body_len":39,"status_code":200,"status_msg":"OK","tags":[],"resp_fuids":["FEEsZS1w0Z0VJIb5x4"],"resp_mime_types":["text/plain"]}'
    }
}
for output.conf.. add stdout {}

sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/



vi filter.conf in my-pipeline directory

filter {
    json {
        source => "[message]"
        target => "[zeek][http]"
        remove_field => "[message]"
    }

}
:w !sudo tee %

run this again 
sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/

filter {
    mutate {
        copy => { "[message]" => "[event][orignial]" }

    }

    }
    json {
        source => "[message]"
        target => "[zeek][http]"
        remove_field => "[message]"
    }
}
:w !sudo tee %
sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/

filter {
    mutate {
        copy => { "[message]" => "[event][orignial]" }

    }

    }
    json {
        source => "[message]"
        target => "[zeek][http]"
        remove_field => "[message]"
    }
    mutate {
        copy => { "[@timestamp]" => "[event][created]" }

    }

    date {
            match => [ "[zeek][http][ts]", "UNIX" ]
            target => "[@timestamp]"
            remove_field => "[zeek][http][ts]"
    }
}
:w !sudo tee %
sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/

filter {
    mutate {
        copy => { "[message]" => "[event][orignial]" }

    }

    }
    json {
        source => "[message]"
        target => "[zeek][http]"
        remove_field => "[message]"
    }
    mutate {
        copy => { "[@timestamp]" => "[event][created]" }

    }

    date {
            match => [ "[zeek][http][ts]", "UNIX" ]
            target => "[@timestamp]"
            remove_field => "[zeek][http][ts]"
    }

    mutate {
            rename => {
                "[zeek][http][id.orig_h]" => "[source][address]"
                "[zeek][http][id.orig_p]" => "[source][ip]"
                "[zeek][http][id.resp_h]" => "[destination][address]"
                "[zeek][http][id.resp_p]" => "[destination][port]"
            }

    }

    mutate {
            copy => {
                "[source][address]" => "[source][ip]"
                "[destination][address]" => "[destination][ip]"
                "[source][address]" => "[client][ip]"
            }
        }


}




filter {
    mutate {
        copy => { "[message]" => "[event][orignial]" }

    }

    }
    json {
        source => "[message]"
        target => "[zeek][http]"
        remove_field => "[message]"
    }
    mutate {
        copy => { "[@timestamp]" => "[event][created]" }

    }

    date {
            match => [ "[zeek][http][ts]", "UNIX" ]
            target => "[@timestamp]"
            remove_field => "[zeek][http][ts]"
    }

    mutate {
            rename => {
                "[zeek][http][id.orig_h]" => "[source][address]"
                "[zeek][http][id.orig_p]" => "[source][ip]"
                "[zeek][http][id.resp_h]" => "[destination][address]"
                "[zeek][http][id.resp_p]" => "[destination][port]"
            }

    }

    mutate {
            copy => {
                "[source][address]" => "[source][ip]"
                "[destination][address]" => "[destination][ip]"
            }
        }

        mutate {
            copy => {
                "[source][address]" => "[source][server]"
                "[destination][address]" => "[destination][server]"
                "[source][port]" => "[client][port]"
                "[destination][port]" => "[server][port]"
            }
        }

        mutate {
            copy => {
                "[client][address]" => "[client][ip]"
                "[server][address]" => "[source][ip]"
            }
        }

        mutate {
                add_field => {
                "[ecs][version] => "1.12"
                "[event][module] => "zeek"
                "[event][dataset] => "zeek.http"
                "[event][kind] => "event"
                }
        }

        ruby {
            code => 'event.set("[event][category]", [ "network", "web"])'
        }

        ruby {
            code => 'event.set("[event][type]", ["connection"])'
        }

}
:w !sudo tee %
sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/