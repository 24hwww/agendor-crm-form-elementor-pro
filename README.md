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
					'section_sendy',
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
						'description' => esc_html__( 'Enter the URL where you have Sendy installed.', 'default' ),
					]
				);		
				$widget->end_controls_section();
			}
			
			public function run( $record, $ajax_handler ) {
				
		$settings = $record->get( 'form_settings' );
				
		$agendor_token = isset($settings['agendor_token']) ? $settings['agendor_token'] : '';

		if ( empty( $agendor_token ) ) {
			return;
		}				
				$raw_fields = $record->get( 'fields' );

				// Normalize form data.
				$fields = [];
				foreach ( $raw_fields as $id => $field ) {
					$fields[ $id ] = $field['value'];
				}		

		/* ENVIAR API AGENDOR */
				wp_remote_post(
					'https://api.example.com/',
					[
						'method' => 'GET',
						'headers' => [
							'Content-Type' => 'application/json',
						],
						'body' => wp_json_encode([
							'site' => get_home_url(),
							'action' => 'Form submitted',
						]),
						'httpversion' => '1.0',
						'timeout' => 60,
					]
				);

			}

			public function on_export( $element ) {}

		}
	}

$form_actions_registrar->register( new \Agendor_Action_After_Submit() );
}
add_action( 'elementor_pro/forms/actions/register', 'add_new_agendor_action_func' );
```
