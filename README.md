# Kafka Airflow Provider

![GitHub release (latest by date)](https://img.shields.io/github/v/release/astronomer/airflow-provider-kafka)![PyPI](https://img.shields.io/pypi/v/airflow-provider-kafka)![PyPI - Downloads](https://img.shields.io/pypi/dm/airflow-provider-kafka)


An airflow provider to: 
- interact with kafka clusters
- read from topics
- write to topics
- wait for specific messages to arrive to a topic

This package currently contains

3 hooks :
- `airflow_provider_kafka.hooks.admin_client.KafkaAdminClientHook` - a hook to work against the actual kafka admin client
- `airflow_provider_kafka.hooks.consumer.KafkaConsumerHook` - a hook that creates a consumer and provides it for interaction
- `airflow_provider_kafka.hooks.producer.KafkaProducerHook` - a hook that creates a producer and provides it for interaction

4 operators : 
- `airflow_provider_kafka.operators.await_message.AwaitKafkaMessageOperator` - a deferable operator (sensor) that awaits to encounter a message in the log before triggering down stream tasks.
- `airflow_provider_kafka.operators.consume_from_topic.ConsumeFromTopicOperator` - an operator that reads from a topic and applies a function to each message fetched. 
- `airflow_provider_kafka.operators.produce_to_topic.ProduceToTopicOperator` - an operator that uses a iterable to produce messages as key/value pairs to a kafka topic. 
- `airflow_provider_kafka.operators.event_triggers_function.EventTriggersFunctionOperator` - an operator that listens for messages on the topic and then triggers a downstream function before going back to listening.

1 trigger : 
- `airflow_provider_kafka.triggers.await_message.AwaitMessageTrigger`


## Quick start

` pip install airflow-provider-kafka` 

Example usages : 
- [basic read/write/sense on a topic](example_dags/listener_dag_function.py)
- [event listener pattern](example_dags/listener_dag_function.py)

## FAQs 

**Why confluent kafka and not (other library) ?** A few reasons: the [confluent-kafka](https://github.com/confluentinc/confluent-kafka-python) library is guaranteed to be 1:1 functional with librdkafka, is faster, and is maintained by a company with a commercial stake in ensuring the continued quality and upkeep of it as a product. 

**Why not release this into airflow directly ?** I could probably make the PR and get it through, but the airflow code base is getting huge and I don't want to burden the maintainers with code that they don't own for maintainence. Also there's been multiple attempts to get a Kafka provider in before and this is just faster. 

**Why is most of the configuration handled in a dict ?** Because that's how `confluent-kafka` does it. I'd rather maintain interfaces that people already using kafka are comfortable with as a starting point - I'm happy to add more options/ interfaces in later but would prefer to be thoughtful about it to ensure that there difference between these operators and the actual client interface are minimal. 

## Local Development

### Unit Tests

Unit tests are located at `tests/unit`, a kafka server isn't required to run these tests.
execute with `pytest`


### Setup on M1 Mac
Installing on M1 chip means a brew install of the `librdkafka` library before you can `pip install confluent-kafka`
```bash
brew install librdkafka
export C_INCLUDE_PATH=/opt/homebrew/Cellar/librdkafka/1.8.2/include
export LIBRARY_PATH=/opt/homebrew/Cellar/librdkafka/1.8.2/lib
pip install confluent-kafka
```
