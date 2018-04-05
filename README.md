# json_transport

Small wrappers to publish JSON data in roscpp and rospy.

## C++

The provided adapters allows natively publishing and subscribing [`nlohmann::json`](https://github.com/nlohmann/json) datatypes.

```
#include "json_transport/json_transport.hpp"

auto sent = R"(
  {
    "this_is_json": true,
    "pi": 3.141
  }
)"_json;

ros::NodeHandle nh;
auto publisher = nh.advertise<nlohmann::json>("json", 1);
publisher.publish(sent);

boost::shared_ptr<const nlohmann::json> received = ros::topic::waitForMessage<nlohmann::json>("json");

assert(*received == sent);
```

## Python

The provided adapters allow publishing anything that can be serialized/deserialized natively via the stdlib `json` module.

```
import json_transport
import rospy

rospy.init_node('json_talker')
pub = json_transport.Publisher('json', queue_size=1, latch=True)
pub.publish(1)
pub.publish([1, 2, 3])
pub.publish({'a': 1, 'b': 2, 'c': 3]


def callback(self, receive):
  pass

json_transport.Subscriber('json', callback=callback)

```

## Notes

- `rospy` doesn't allow type masquerading, but there should be away to deserialize without an intermediary representation using [`rospy.AnyMsg`](http://docs.ros.org/api/rospy/html/rospy.msg.AnyMsg-class.html). Documentation is sparse.
- `nlohmann::json` supports binary formats (CBOR, MessagePack, and UBJSON) which would make transport and serialization/deserialization more efficient. For some reason I'm unable to get any of those working with their python library equivalents (cbor2, msgpack, and ubjson) due to `nlohmann::json` not generating a 4-byte object header.
