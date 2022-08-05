# Implementing the Server

```py
from concurrent import futures

import grpc
import bidirectional.bidirectional_pb2_grpc as bidirectional_pb2_grpc


class BidirectionalService(bidirectional_pb2_grpc.BidirectionalServicer):

    def GetServerResponse(self, request_iterator, context):
        for message in request_iterator:
            yield message


def serve():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    bidirectional_pb2_grpc.add_BidirectionalServicer_to_server(BidirectionalService(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()


if __name__ == '__main__':
    serve()
```

# Implementing the Client

```py
from __future__ import print_function

import grpc
import bidirectional.bidirectional_pb2_grpc as bidirectional_pb2_grpc
import bidirectional.bidirectional_pb2 as bidirectional_pb2


def make_message(message):
    return bidirectional_pb2.Message(
        message=message
    )


def generate_messages():
    messages = [
        make_message("First message"),
        make_message("Second message"),
        make_message("Third message"),
        make_message("Fourth message"),
        make_message("Fifth message"),
    ]
    for msg in messages:
        print("Hello Server Sending you the %s" % msg.message)
        yield msg


def send_message(stub):
    responses = stub.GetServerResponse(generate_messages())
    for response in responses:
        print("Hello from the server received your %s" % response.message)


def run():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = bidirectional_pb2_grpc.BidirectionalStub(channel)
        send_message(stub)


if __name__ == '__main__':
    run()
```


# Stub과 Servicer

    Stub

    