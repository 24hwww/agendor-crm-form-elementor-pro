# Agendor CRM form Elementor PRO
Agendor.com.br CRM - integração com form feita no Elementor Pro

```php
function add_new_agendor_action_func( $form_actions_registrar ) {

	if (!class_exists('Agendor_Action_After_Submit')) {	
		class Agendor_Action_After_Submit extends \ElementorPro\Modules\Forms\Classes\Action_Base {

			public function get_name() {
				return 'agendor';
			}
			public function get_label() {
				return esc_html__( 'Agendor', 'elementor-forms-ping-action' );
			}

			public function register_settings_section( $widget ) {
				
			$widget->start_controls_section(
					'agendor_section',
					[
						'label' => esc_html__( 'Agendor', 'default' ),
						'condition' => [
							'submit_actions' => $this->get_name(),
						],
					]
				);
			
				
					$widget->add_control(
					'agendor_token',
					[
						'label' => esc_html__( 'Token', 'default' ),
						'type' => \Elementor\Controls_Manager::TEXT,
						'placeholder' => '',
						'description' => 'To authenticate your requests, you need an API token. You can find your token by logging into Agendor, and going to <a href="https://web.agendor.com.br/sistema/integracoes" target="_blank">Menu > Integrações.</a> This token is like your password, so keep it secret!',
					]
				);		
				$widget->end_controls_section();
			}
			
public function run( $record, $ajax_handler ) {
			
$settings = $record->get( 'form_settings' );
$raw_fields = $record->get( 'fields' );
				
$agendor_token = isset($settings['agendor_token']) ? $settings['agendor_token'] : '';				

//Normalize_form_data.
$fields = [];
if(is_array($raw_fields) && count($raw_fields) > 0){
foreach($raw_fields as $id => $field){
$fields[$id] = $field['value'];
}
}

$form_geral_nome = isset($fields['form_geral_nome']) ? $fields['form_geral_nome'] : '';
$form_geral_email = isset($fields['form_geral_email']) ? $fields['form_geral_email'] : '';
$form_geral_telefone = isset($fields['form_geral_telefone']) ? $fields['form_geral_telefone'] : '';
$form_custom_field_finalidade = isset($fields['field_e762c4a']) ? $fields['field_e762c4a'] : '';
$leadOrigin = 'Site Smartbox';
$finalidade = "Finalidade: {$form_custom_field_finalidade}";
	
$url_api = "https://api.agendor.com.br/v3/people";

$data = array(
'name' => $form_geral_nome,
"contact" => array(
"email" => $form_geral_email,
"mobile" => $form_geral_telefone
),
'leadOrigin' => $leadOrigin,	
'description' => $finalidade
);
$data = json_encode($data);
	
$curl = curl_init();
curl_setopt($curl, CURLOPT_HTTPHEADER, array("Authorization: Token {$agendor_token}","Content-Type: application/json"));
curl_setopt($curl, CURLOPT_URL,$url_api);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, TRUE);
curl_setopt($curl, CURLOPT_VERBOSE, 0);
curl_setopt($curl, CURLOPT_POSTFIELDS, $data);

$response = curl_exec($curl);
curl_close($curl);
				
			}

			public function on_export( $element ) {
				
				unset($element['agendor_token']);
				return $element;
				
			}

		}
	}

$form_actions_registrar->register( new \Agendor_Action_After_Submit() );
}
add_action( 'elementor_pro/forms/actions/register', 'add_new_agendor_action_func' );
```
