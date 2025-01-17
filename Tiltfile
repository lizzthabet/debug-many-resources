load('ext://uibutton', 'cmd_button', 'location', 'text_input')

k8s_yaml('a-b.yml')

k8s_resource(
  'a',
  new_name='frontend-combo',
  port_forwards='6363:80',
  objects=['c'],
  labels="frontend"
)

k8s_resource(
  'b',
  new_name='database',
  labels=["backend", "storage"]
)

k8s_yaml('d.yml')

k8s_resource('d',
  new_name='backend',
  resource_deps=['frontend-combo'],
  labels="backend"
)

local_resource('hello', 'sleep $(($RANDOM % 5)); echo hi;exit 0', 'foo.txt', allow_parallel=True)
local_resource('goodbye', 'echo bye; exit 1', 'foo.txt', allow_parallel=True)

# Generate a ton of resources
for i in range(1, 90):
  resource_name = 'resource' + str(i)
  resource_yaml = read_yaml('d.yml')
  resource_yaml['metadata']['name'] = resource_name
  resource_yaml['spec']['selector']['matchLabels']['app'] = resource_name
  resource_yaml['spec']['template']['metadata']['labels']['app'] = resource_name
  resource_yaml['spec']['template']['spec']['containers'][0]['name'] = resource_name
  k8s_yaml(encode_yaml(resource_yaml))
  k8s_resource(
    resource_name,
    new_name='resource_' + str(i),
    labels=["generated"]
  )

# Only enable a selection of resources
enabled_resources=['frontend-combo', 'database', 'backend', 'hello', 'goodbye']

for i in range(1, 10):
  enabled_resources.append('resource_' + str(i))

config.set_enabled_resources(enabled_resources)

# Add some buttons
cmd_button('btn1', resource='hello', argv=['echo', 'foo'], text='Foo!', icon_name='travel_explore')

cmd_button('nav', location=location.NAV, argv=['echo', 'foo'], text='Reseed Database', icon_name='travel_explore', inputs=[text_input('shard')])

cmd_button('echo', resource='goodbye', argv=['/bin/sh', '-c', 'echo $MESSAGE'], text='Echo', inputs=[text_input('MESSAGE')])

cmd_button('hello',
           argv=['sh', '-c', 'echo Hello, $NAME'],
           location=location.NAV,
           icon_name='front_hand',
           text='Hello!',
           inputs=[
             text_input('NAME', placeholder='Enter your name'),
           ]
)

# disable_feature('disable_resources')