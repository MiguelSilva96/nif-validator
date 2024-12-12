# NIF Validator
Python NIF Validator

## Build

    docker build -t nif-validator .

## Deliver

    docker tag nif-validator cfreire70/nif-validator
    docker login -u username -p password
    docker push cfreire70/nif-validator

## Run

    docker run -d --name nif-validator -p 8000:9046 cfreire70/nif-validator

## TTL.sh

    $ IMAGE_NAME=$(date | md5sum | cut -f 1 -d' ')
    $ docker build -t ttl.sh/${IMAGE_NAME}:1h .
    $ docker push ttl.sh/${IMAGE_NAME}:1h
    
    ................................................
    image ttl.sh/xxxx-yyyy-nnnn-2a2222-4b44 is available for 1 hour
    
    ttl.sh is contributed by Replicated (www.replicated.com)
