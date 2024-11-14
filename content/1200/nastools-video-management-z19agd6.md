<h1>Nas-Tools（影音管理）</h1>
<pre><code class="language-bash"># nas-tools admin password
# --mount type=bind,source=/mnt/nas_share,target=/media \
docker run -itd --name nas-tools \
--hostname nas-tools \
-p 12221:3000  \
-v /opt/nastools/config:/config \
-v /mnt/disk/video/sourc/film:/media/source/film \
-v /mnt/disk/video/source/tv:/media/source/tv \
-v /mnt/disk/video/link/film:/media/link/film \
-v /mnt/disk/video/link/tv:/media/link/tv \
-e PUID=0 \
-e PGID=0 \
-e UMASK=000 \
-e NASTOOL_AUTO_UPDATE=false \
-e NASTOOL_CN_UPDATE=false \
--restart unless-stopped \
jxxghp/nas-tools:latest
</code></pre>
<pre><code class="language-bash"># 家庭影音
cat &gt; docker-compose-pve.yml &lt;&lt; &quot;EOF&quot;
version: '3.3'
services:
    # 资源刮削
    nas-tools:
        container_name: nas-tools
        hostname: nas-tools
        ports:
            - '12221:3000'
        volumes:
            - '/opt/nastools/config:/config'
            - '/mnt/disk/video/sourc/film:/media/source/film'
            - '/mnt/disk/video/source/tv:/media/source/tv'
            - '/mnt/disk/video/link/film:/media/link/film'
            - '/mnt/disk/video/link/tv:/media/link/tv'
        environment:
            - PUID=0
            - PGID=0
            - UMASK=000
            - NASTOOL_AUTO_UPDATE=false
            - NASTOOL_CN_UPDATE=false
        restart: unless-stopped
        image: 'ljnpng/nastools:2.9.2'
    # 媒体影音
    jellyfin:
        container_name: jellyfin
        restart: always
        environment:
            - PUID=root
            - PGID=root
        ports:
            - '12222:8096'
        volumes:
            - '/opt/jellyfin/config:/config/'
            - '/mnt/disk/video/sourc/film:/media/source/film'
            - '/mnt/disk/video/source/tv:/media/source/tv'
            - '/mnt/disk/video/link/film:/media/link/film'
            - '/mnt/disk/video/link/tv:/media/link/tv'
        devices:
            - '/dev/dri/renderD128:/dev/dri/renderD128'
            - '/dev/dri/card0:/dev/dri/card0'
        image: 'nyanmisaka/jellyfin:latest'
    # 下载器
    qbittorrentee:
        container_name: qb
        environment:
            - WEBUIPORT=8080
            - PUID=0
            - PGID=0
            - TZ=Asia/Shanghai
        ports:
            - '6881:6881'
            - '6881:6881/udp'
            - '12223:8080'
        volumes:
            - '/opt/qbittorrent/config/:/config'
            - '/mnt/disk/video/sourc/film:/media/source/film'
            - '/mnt/disk/video/source/tv:/media/source/tv'
            - '/mnt/disk/video/link/film:/media/link/film'
            - '/mnt/disk/video/link/tv:/media/link/tv'
        restart: unless-stopped
        image: 'superng6/qbittorrentee:latest'
    # 自动化字幕    
    chinesesubfinder:
        container_name: chinesesubfinder
        restart: always
        environment:
            - PUID=0
            - PGID=0
            - TZ=Asia/Shanghai
        ports:
            - '12224:19035'
        volumes:
            - '/opt/chinesesubfinder/config:/config'
            - '/opt/chinesesubfinder/cache:/app/SubFixCache'
            - '/mnt/disk/video/source/film/:/media/source/film'
            - '/mnt/disk/video/source/tv:/media/source/tv'
            - '/mnt/disk/video/link/film/:/media/link/film'
            - '/mnt/disk/video/link/tv:/media/link/tv'
        image: 'allanpk716/chinesesubfinder:latest'
    # 索引器
    jackett:
        container_name: jackett
        environment:
            - PUID=0
            - PGID=0
            - TZ=Asia/Shanghai
            - AUTO_UPDATE=true
        ports:
            - '12225:9117'
        volumes:
            - '/opt/jackett:/config'
        restart: unless-stopped
        image: 'linuxserver/jackett:latest'
EOF
docker-compose -f docker-compose-pve.yml up -d
docker ps
</code></pre>
<pre><code class="language-纯文本">


docker run -itd --name showdoc --restart always --user=root --privileged=true -p 18883:80 -v D:\software\ShowDoc:/var/www/html/ star7th/showdoc
docker run --name=gitlab -itd --publish 18884:80 --restart always --volume D:\software\gitlab\configs:/etc/gitlab --volume D:\software\gitlab\logs:/var/log/gitlab --volume D:\software\gitlab\data:/var/opt/gitlab gitlab/gitlab-ce:latest


</code></pre>
<pre><code class="language-纯文本">docker run --name=gitlab -itd \
-p 18884:80 \
--user=root \
--privileged=true \
-v /home/kentbryce/gitlab/configs:/etc/gitlab \
-v /home/kentbryce/gitlab/logs:/var/log/gitlab \
-v /home/kentbryce/gitlab/data:/var/opt/gitlab \
gitlab/gitlab-ce:15.11.1-ce.0

docker cp /home/kentbryce/gitlab/configs gitlab:///etc/gitlab
docker cp /home/kentbryce/gitlab/logs gitlab:///var/log/gitlab
docker cp /home/kentbryce/gitlab/data gitlab:///var/opt/gitlab


docker stop gitlab &amp;&amp; docker rm gitlab
docker run --detach \
--publish 8443:443 \
--publish 18884:80 \
--name gitlab \
--user=root \
--privileged=true \
--restart unless-stopped \
-v /home/kentbryce/gitlab/configs:/etc/gitlab \
-v /home/kentbryce/gitlab/logs:/var/log/gitlab \
-v /home/kentbryce/gitlab/data:/var/opt/gitlab \
gitlab/gitlab-ce:15.11.1-ce.0


docker run -it \
    -v /data/software/zentao/data:/data \
    -p 18888:80 \
    -e MYSQL_INTERNAL=false \
    -e ZT_MYSQL_HOST=127.0.0.1 \
    -e ZT_MYSQL_PORT=3306 \
    -e ZT_MYSQL_USER=root \
    -e ZT_MYSQL_PASSWORD=root \
    -e ZT_MYSQL_DB=zentao \
    hub.zentao.net/app/zentao


</code></pre>
